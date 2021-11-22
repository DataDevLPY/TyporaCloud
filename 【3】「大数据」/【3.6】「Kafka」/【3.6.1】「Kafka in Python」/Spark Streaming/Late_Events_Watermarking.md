## FIT5202 : Week 11 - Spark Structured Streaming

In this lab today, we will look into additional concepts in Spark Structured Streaming like, using File-Sink, implementing windowed aggregations on the streams, handling late event-time data and checkpointing.

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
from pyspark.sql.functions import *


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

### Step 3

Converting the key/value from the kafka data stream to string

```python
df = df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
```



### Step 4

Converting the incoming stream into dataframe based on the schema. Note that, were are receiving a list from the producer, so the schema and other transformations are done accordingly

```python
#Define the schema for the structured datastream received
schema = ArrayType(StructType([    
    StructField('Clicks', IntegerType(), True), 
    StructField('Impressions', IntegerType(), True),
    StructField('ts', TimestampType(), True)            
]))
```

```python
df=df.select(F.from_json(F.col("value").cast("string"), schema).alias('parsed_value'))
df = df.select(F.explode(F.col("parsed_value")).alias('unnested_value'))  
df_formatted = df.select(
                    F.col("unnested_value.Clicks").alias("Clicks"),
                    F.col("unnested_value.Impressions").alias("Impressions"),
                    F.col("unnested_value.ts").alias("ts")
                )
```

```python
df_formatted.printSchema()
```

![截屏2021-01-23 下午8.39.48](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午8.39.48.png)

### Write stream output to a parquet file ([File Sink](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html#output-sinks)) 

Parquet is a columnar file format that provides optimizations to speed up queries and is far more efficient file format than csv or json. We can `format("parquet")` to save the query to the parquet file.

```python
# Write into parquet files the unsuccessful requests partitioned by status code
query_file_sink = df_formatted.writeStream.format("parquet")\
        .outputMode("append")\
        .option("path", "parquet/clickstream_df")\
        .option("checkpointLocation", "parquet/clickstream_df/checkpoint")\
        .start()
```

```python
#Stop the file_sink query
query_file_sink.stop()
```

#### Read the saved parquet data

```python
# Read the saved parquet data
query_file_sink_df = spark.read.parquet("parquet/clickstream_df")
query_file_sink_df.printSchema()
query_file_sink_df.show()
```

Here, we have defined a 10 seconds window, to count the number of impressions every 10 seconds. 5 seconds is the overlapping window.



### Aggregations on Windows over Event-time 

Instead of running aggregations over the whole stream, we can aggregate over data bucketed by time window (e.g. every 10 secs). For example, in our use case, we want to calculate the total impressions received by the ad in the last 10 seconds. This 10 second window will be based on the timestamp embedded in the data i.e. `ts` which we have generated on the producer's side and not the time it is being processed (aka. processing-time).

```python
windowedCounts = df_formatted \
    .groupBy(window(df_formatted.ts, "10 seconds"))\
    .agg(F.sum("Impressions").alias("total"))\
    .select("window","total")
```



### Handling Late Events with Watermarking [[Ref\]](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html#handling-late-data-and-watermarking)

When we are considering event-time, sometimes the data may arrive later than expected. However, we cannot include the data in our current window aggregate if it doesn't fall in the window range. The previous aggregate i.e. old window, where the data falls into needs to be updated appropriately. The concept of **Watermarking** lets the engine automatically track the current event time in the data and update the aggregate window and the states accordingly.

We can define the watermark of a query by specifying the event time column and the threshold on how late the data is expected to be in terms of eventtime.

```python
windowedCounts = df_formatted \
    .withWatermark("ts", "10 seconds") \
    .groupBy(window(df_formatted.ts, "10 seconds"))\
    .agg(F.sum("Impressions").alias("total"))\
    .select("window","total")
```

```python
query = windowedCounts \
    .writeStream \
    .outputMode("complete") \
    .format("memory") \
    .queryName('df_formatted')\
    .trigger(processingTime='5 seconds') \
    .option("truncate","false")\
    .start()
```

```python
spark.sql('select * from df_formatted').show()
```

![截屏2021-01-23 下午9.07.14](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午9.07.14.png)

```
query.stop()
```



