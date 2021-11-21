### Step 1

Initialize Spark Session

```python
import os
os.environ['PYSPARK_SUBMIT_ARGS'] = '--packages org.apache.spark:spark-streaming-kafka-0-10_2.12:3.0.0,org.apache.spark:spark-sql-kafka-0-10_2.12:3.0.0 pyspark-shell'

from pyspark.sql import SparkSession
from pyspark.sql.functions import explode
from pyspark.sql.functions import split
from pyspark.sql import functions as F
from pyspark.sql.types import *

spark = SparkSession \
    .builder \
    .appName("Clickstream Analysis in Spark") \
    .getOrCreate()

```

### Step 2

Connection to Kafka Producer/Broker and subscribe to the topic and load data from Kafka topic with `readStream`

```python
topic = "clickstream"
df = spark \
    .readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", "127.0.0.1:9092") \
    .option("subscribe", topic) \
    .load()
```



```python
df.printSchema()
```

![截屏2021-01-23 下午1.30.43](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午1.30.43.png)

### Step 3

Converting the key/value from the kafka data stream to string

```python
df = df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
```

See the output in the console to examine the data after casting to string

```python
query = df \
    .writeStream \
    .outputMode("append") \
    .format("console") \
    .trigger(processingTime='5 seconds') \
    .start()
```



```python
query.stop()
```

```python
df.printSchema()
```

![截屏2021-01-23 下午1.32.26](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午1.32.26.png)



### Step 4

Define a schema according to our data (as sent from the producer), Use `from_json` to parse the string to the json format based on the defined schema. Depending on how you are sending the data, you can define a DataFrame Schema on the receiver's side. For example if you are sending a json list from the producer with "Clicks", "Impressions" and "ts" fields, you can define a schema as follows

```python
schema = StructType([    
    StructField('Clicks', IntegerType(), True), 
    StructField('Impressions', IntegerType(), True),
    StructField('ts', TimestampType(), True)            
])
```

```python
df=df.select(F.from_json(F.col("value").cast("string"), schema).alias('parsed_value'))
```

```python
df.printSchema()
```

![截屏2021-01-23 下午1.33.15](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午1.33.15.png)

The columns need to be renamed appropriately.

```python
df_formatted = df.select(
                    F.col("parsed_value.Clicks").alias("Clicks"),
                    F.col("parsed_value.Impressions").alias("Impressions"),
                    F.col("parsed_value.ts").alias("ts")
                )
```

```python
df_formatted.printSchema()
```

![截屏2021-01-23 下午1.33.59](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午1.33.59.png)

### Step 5

Create the **output sink** for the stream. Click on the following links to learn more about the relevant topics.

| **Output Modes** | [Link](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html#output-modes) |
| ---------------- | ------------------------------------------------------------ |
| **Output Sinks** | [Link](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html#output-sinks) |
| **Triggers**     | [Link](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html#triggers) |

```python
query = df_formatted \
    .writeStream \
    .outputMode("append") \
    .format("console") \
    .trigger(processingTime='5 seconds') \
    .start()
```

```python
query.stop()
```



### Operations on streaming DataFrames [[Ref\]](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html#operations-on-streaming-dataframesdatasets)

You can apply all kinds of operations on streaming DataFrames/Datasets – ranging from untyped, SQL-like operations (e.g. select, where, groupBy), to typed RDD-like operations (e.g. map, filter, flatMap).

The data we have been recieving has "clicks", "impressions" and "timestamps". We now would like to group the total number of impressions for each minute. We can apply this transformation to the readstream query before writing it to the output sink.

```python
#Using the .minute function, we can perform the following aggregation 
grouped_by_min = df_formatted.groupBy(F.minute("ts").alias("minute_bin"))\
                    .agg(F.sum("Impressions").alias("Total Impressions"))            
```

```python
grouped_by_min.printSchema()
```

![截屏2021-01-23 下午1.36.14](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午1.36.14.png)

### Visualizing streaming data

We have implemented the aggregation to get number of impressions every minute. Let's write this this to the memory sink and query it using spark sql for visualizing it in real time.

Note that, since we are using "aggregated query", we need use "complete" as the output mode.

**You can see the output every 5 seconds in the console as well**

```python
#Change the output sink to "memory" and write output to the memory sink
query = grouped_by_min \
    .writeStream \
    .outputMode("complete") \
    .format("memory") \
    .queryName("impressions_minute_bin") \
    .trigger(processingTime='5 seconds') \
    .start()
```

```python
spark.sql("select * from impressions_minute_bin").show()
```

![截屏2021-01-23 下午1.37.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午1.37.01.png)



Here, first we need to initialize an empty plot.

```python
def init_plots():
    try:
        width = 9.5
        height = 6
        fig = plt.figure(figsize=(width,height)) # create new figure
        ax = fig.add_subplot(111) # adding the subplot axes to the given grid position
        fig.suptitle('Real-time uniform stream data visualization') # giving figure a title
        ax.set_xlabel('Time')
        ax.set_ylabel('Value')        
        fig.show() # displaying the figure
        fig.canvas.draw() # drawing on the canvas
        return fig, ax
    except Exception as ex:
        print(str(ex))
```



```python
import time
import matplotlib.pyplot as plt
%matplotlib notebook

fig, ax = init_plots()

while True:
    df = spark.sql("select * from impressions_minute_bin order by minute_bin desc limit 10").toPandas()
    
    x = df['minute_bin'].to_list()
    y = df['Total Impressions'].to_list() 
    ax.clear()
    print(x)
    print(y)
    ax.plot(x, y)
    ax.set_xlabel('Time')
    ax.set_ylabel('Impressions')    
    fig.canvas.draw()
    
    time.sleep(1)
    
```

















