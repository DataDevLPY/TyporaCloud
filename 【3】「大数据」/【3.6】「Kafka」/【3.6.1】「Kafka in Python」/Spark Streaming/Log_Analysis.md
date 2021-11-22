### Step 1 : Include Libraries and Initialize Spark Session[¶](http://localhost:8889/notebooks/Desktop/FIT5202 Data Processing for Big Data/week 10/Tutorial/2. Lab-Task Log Analysis [V 1.1].ipynb#Step-1-:-Include-Libraries-and-Initialize-Spark-Session)

```python
#Import necessary libraries and initialize Spark Session
import os
os.environ['PYSPARK_SUBMIT_ARGS'] = '--packages org.apache.spark:spark-streaming-kafka-0-10_2.12:3.0.0,org.apache.spark:spark-sql-kafka-0-10_2.12:3.0.0 pyspark-shell'

from pyspark.sql import SparkSession
from pyspark.sql.functions import explode
from pyspark.sql.functions import regexp_extract
from pyspark.sql.functions import split
from pyspark.sql import functions as F
from pyspark.sql.types import *

spark = SparkSession \
    .builder \
    .appName("Lab-Task") \
    .getOrCreate()
```

## Use-Case : Tracking Server Access Log 

For this case, a server is going to continuously send a records of a host who is trying to access some endpoint (url) from the web server. This data will be send from a kafka producer (`2.Lab-Task-KafkaProducer.ipynb`) which is reading the data from a txt file in the dataset provided (`logs/access_log.txt`).

Each line contains some valuable information such as:

1. Host
2. Timestamp
3. HTTP method
4. URL endpoint
5. Status code
6. Protocol
7. Content Size

The goal here is to perform some real time queries from this stream of data and be able to output the results in multiple ways.

### Step 2 : Load Kafka Stream

Use the `readStream` to load data from the Kafka Producer **2.Lab-Task-KafkaProducer.ipynb**

```python
# Monitor the logs data stream for new log data
topic = "w10_access_log"
#df_urls = #WRITE THE CODE HERE
df_urls = spark \
    .readStream \
    .format("kafka") \
    .option("kafka.bootstrap.servers", "127.0.0.1:9092") \
    .option("subscribe", topic) \
    .load()
```

```
df_urls.printSchema()
```

![截屏2021-01-23 下午3.03.36](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午3.03.36.png)



## Data Preparation 

We need to convert the data from the message in order to perform some queries. The steps to parse the data are:

1. Get message as a string from `value` which is binary.
2. Implement some regular expressions to capture specific fields in the message which is a line from the access log.
3. Extract the values using the regular expressions to create the dataframe.

```python
# Get value of the kafka message
log_lines = df_urls.selectExpr("CAST(value AS STRING)")

# Parse out the common log format to a DataFrame
statusExp = r'\s(\d{3})\s'
generalExp = r'\"(\S+)\s(\S+)\s*(\S*)\"'
hostExp = r'(^\S+\.[\S+\.]+\S+)\s'

df_logs = log_lines.select(regexp_extract('value', hostExp, 1).alias('host'),
                         regexp_extract('value', generalExp, 1).alias('method'),
                         regexp_extract('value', generalExp, 2).alias('endpoint'),
                         regexp_extract('value', generalExp, 3).alias('protocol'),
                         regexp_extract('value', statusExp, 1).cast('integer').alias('status'))

df_logs.printSchema()
```

![截屏2021-01-23 下午3.04.53](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-23 下午3.04.53.png)

## Data Streaming Processing

```python
# 1. DF that filters those requests that were not successful (status != 200)
unsucess_df = df_logs[df_logs['status'] != 200]
```

```python
# 2. DF that keeps a running count of every access by status code
status_count_df = df_logs.groupby(F.col('status')).count()
```



## Output sink 

Before starting this section, run the kafka producer (`2.Lab-Task-KafkaProducer.ipynb`) that will send the data from the access log file.

```python
# Create function to show values received from input dataframe
def foreach_batch_function(df, epoch_id):
    df.show(20,False)
```

#### Display stream output in notebook 

```python
# Write output of status_count_df in output cell using the foreach_batch_function
# Control the amount of times output is displayed with trigger function
query1 = status_count_df.writeStream.outputMode("complete")\
        .foreachBatch(foreach_batch_function)\
        .trigger(processingTime='5 seconds')\
        .start()
# So big
#query2 = unsuccess_df.writeStream.outputMode("append")\
#        .foreachBatch(foreach_batch_function)\
#        .trigger(processingTime='5 seconds')\
#        .start()
```

```
query1.stop()
```

Write the stream output to the **memory sink** and display the result using **spark SQL**

```python
#WRITE THE CODE HERE TO WRITE OUTPUT TO MEMORY SINK
query = status_count_df \
    .writeStream \
    .outputMode("complete") \
    .format("memory") \
    .queryName("status_count1") \
    .trigger(processingTime='5 seconds') \
    .start()
```

```python
#WRITE THE CODE HERE TO QUERY THE TABLE FROM MEMORY SINK
spark.sql("select * from status_count1").show()
```

```
query.stop()
```















