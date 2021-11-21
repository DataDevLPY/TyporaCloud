# Dataframe in Spark

A DataFrame is a distributed collection of data organized into named columns. It is equivalent to a table in relational database or a dataframe in R/Python but with richer optimizations under the hood. For more information visit :

https://docs.databricks.com/spark/latest/dataframes-datasets/introduction-to-dataframes-python.html

## Creating DataFrames

SparkSession provides an easy method `createDataFrame` to create Spark DataFrames. Data can be loaded from csv, json, xml and other sources like local file system or HDFS. More information on :

https://spark.apache.org/docs/latest/api/python/pyspark.sql.html

```python
## First method
df = spark.createDataFrame([(1,'Aaditya','A'),(2,'Chinnavit','C'),(3,'Neha','N'),(4,'Huashun','H'),(5,'Mohammad','M'),(10,'Prajwol', 'P'),(1,'Paras','P'),(1, 'Tooba','T'),(3, 'David','D'),(4,'Cheng','C'),(9,'Haqqani','H')],
                           ['Id','Name','Initial'])
# To show column names
df.printSchema()

## Second method
df = spark.read.csv("bank.csv",header = True, inferShema = True)

## Third method
df = spark.read.format('csv').option('header', True).option('escape', '"')\
			.load('file_name.csv')
```



To set the Schema

```python
## To set the appropriate data type of all of schema

## Since columns 'MINFLT', 'MAJFLT', 'VSTEXT', 'RSIZE', 'VGROW', 'RGROW' has some values which not numeric
schema_memory = StructType([StructField("ts",IntegerType()),
                            StructField("PID",IntegerType()),
                            StructField("MINFLT",StringType()),
                            StructField("MAJFLT",StringType()),
                            StructField("VSTEXT",StringType()),
                            StructField("VSIZE",StringType()),
                            StructField("RSIZE",StringType()),
                            StructField("VGROW",StringType()),
                            StructField("RGROW",StringType()),
                            StructField("MEM",DoubleType()),
                            StructField("CMD",StringType()),
                            StructField("attack",IntegerType()),
                            StructField("type",StringType())
                            ])

schema_process = StructType([StructField("ts",IntegerType()),
                             StructField("PID",IntegerType()),
                             StructField("TRUN",IntegerType()),
                             StructField("TSLPI",IntegerType()),
                             StructField("TSLPU",IntegerType()),
                             StructField("POLI",StringType()),
                             StructField("NICE",IntegerType()),
                             StructField("PRI",IntegerType()),
                             StructField("RTPR",IntegerType()),
                             StructField("CPUNR",IntegerType()),
                             StructField("Status",StringType()),
                             StructField("EXC",IntegerType()),
                             StructField("State",StringType()),
                             StructField("CPU",DoubleType()),
                             StructField("CMD",StringType()),
                             StructField("attack",IntegerType()),
                             StructField("type",StringType())
                            ])
```



## Dataframe Methods

```python
# show the column of the header
df.columns
df.printSchema()
# show the count of row
df.count()
# show the description of each column
df.describe('column_name').show()
df.describe().toPandas()

# selecting
df.select('column_name').distinct().show()

# Filtering
df.filter(df.column_name == 123).show()
```



## Partitioning in DataFrames

```python
# Round-robin data partitioning
df_round = df.repartition(num)
# Range data partitioning
df_range = df.repartitionByRange(num, "column_name")
# Has data partitioning,int
column_hash = "column_name"
df_hash = df.repartition(column_hash)

## Print the number of partitions for each dataframe
print(f"----- NUMBER OF PARTITIONS df_round: {df_round.rdd.getNumPartitions()}")
print(f"----- NUMBER OF PARTITIONS df_range: {df_range.rdd.getNumPartitions()}")
print(f"----- NUMBER OF PARTITIONS df_hash: {df_hash.rdd.getNumPartitions()}")
```

Query plan:

```python
## You can verify the partitioning and the query plan when an action is performed with the function explain()
# Query plan for df_round
df_round.explain()
# Query plan for df_range
df_range.explain()
# Query plan for df_hash
df_hash.explain()
```



## Parallel Join Strategies

1. Broadcast Hash Join

   By doing this, we can avoid the shuffle for the other larger dataset. Not doing the shuffle increase the speed of the join operation.

   ```python
   from pyspark.sql.functions import broadcast
   df_joinded_broadcast = df_B.join(broadcast(df_A), df_A.id == df_B.id, how = 'inner')
   ```

2. Sort Merge Join

   The order of execution goes from top to bottom. The steps are:

   1). Scan dataframe A (left side)

   - Filter id not null in dataframe A

   2). Scan dataframe B (right side)

   - Filter id not null in dataframe B

   3). Exchange dataframe A: Partition dataframe A with hash partitioning

   4). Exchange dataframe B: Partition dataframe B with hash partitioning

   5). Sort dataframe A: Sort data within each partition

   6). Sort dataframe B: Sort data within each partition

   7). Perform Sort Merge Join between both dataframes

   8). Project: Select the attributes from both dataframes (df_A: id,valueA and df_b: id,valueB)

   9). Collect all the results to the driver

   ```python
   joined_sortmerge = df_A.join(df_B, df_A.id == df_B.id, how == 'inner')
   ```

3. Left Join

   This join operation returns all records from the left dataframe and the matched records from the right dataframe.

   ```python
   left_join = df_A.join(df_B, df_A.id == df_B.id, how = 'left')
   ```

4. Full Outer Join

   This join operation returns a result set that includes rows from both left and right dataframes.

   ```python
   outer_join = df_A.join(df_B, df_A.id == df_B.id, how = 'outer')
   ```

5. Left Semi Join

   This join operation is like an inner join, but only the left dataframe columns and values are selected

   ```python
   left_semi_join = df_A.join(df_B, df_A.id == df_B.id, how = 'left_semi')
   ```

6. Left Anti Join

   This join operation is the difference of the left dataframe minus the right dataframe, as it selects all rows from df1 that are not present in df2

   ```python
   left_anti_join = df_A.join(df_B, df_A.id == df_B.id, how = 'left_anti')
   ```

   

## Parallel Aggregation

```python
import pyspark.sql.functions as F
```



1. Group By

   ```python
   agg_attribute = 'Year'
   ## First
   		# count, sum, max, avg, min
   df.groupby(agg_attribute).agg(F.count(agg_attribute).alias('Total'))
   ## Second
   df.groupby(agg_attribute).agg({'Year':'Count'})
   ```

2. Sort By

   ```python
   ## First
   		# aesc
   df.select('*').orderBy(df.Year)
   		# desc
   df.select('*').orderBy(df.Year.desc())
   
   ## Second
   		# aesc
   df.select('','').sort('Year', ascending = True)
   		# desc
   df.select('','').sort('Year', ascending = False)
   ```

3. Adding a column

   ```python
   df_new = df.withColumn('Years ago', 2020-df.Year)
   ```

4. Dropping a column

   ```python
   # .drop('column_name')
   df_drop = df.drop('Years ago')
   ```



## Functions

```python
from pyspark.sql import functions as F
from pyspark.sql.types import IntegerType
```



1. Pyspark functions

   ```python
   # col() and cast() and split()
   df = df.withColumn('Age', F.col('Age')).cast(IntegerType())
   
   df = df.withColumn('Game Year', F.split(df.Games, ' ')[0])
   
   # when and otherwise
   # Change the status column to numeric: ham to 1.0 and spam to 0. 
   from pyspark.sql.functions import when
   df = df.withColumn('label', when(df['label'] == 'ham', 1.0).otherwise(0.0))
   df.show(5, truncate = False)
   
   ## column rename
   df = df.withColumnRenamed('_c0', 'label').withColumnRenamed('_c1', 'message')
   
   # cache()
   # 使用默认存储级别（MEMORY_AND_DISK）持久保存DataFrame。
   
   # corr()
   # 以双精度值计算DataFrame的两列的相关性。
   # cov()
   # 计算给定列的样本协方差（由它们的名称指定）作为双精度值。
   ```

   ```python
   # To find non-numeric or numeric columns
   numeric_cols = []
   for col in df.schema:
     	if isinstance(col.datatype, IntegerType) or isinstance(col.datatype, DoubleType):
         	numeric_cols.append(col.name)
   ```

   

2. User Defined Functions

   ```python
   #1. The function is defined
   def extract_year(s):
       return int(s.split(' ')[0])
   
   #2. Calling the UDF with DataFrame
   from pyspark.sql.functions import udf
   from pyspark.sql.types import IntegerType
   
   #First Register the function as UDF
   extract_year_udf = udf(extract_year,IntegerType())
   
   #Call the function
   df_events.select('Games',extract_year_udf('Games').alias("Game Year")).show(5)
   
   #4. Calling with Spark SQL
   #First Register the function as UDF
   spark.udf.register('extract_year',extract_year,IntegerType())
   ```

   

## Writing in csv

```python
df.write.format('.csv').option('header', True).mode('overwrite').option('sep',',')\
		.save('df.csv')
```

