# Assignment 2

# 1. Data preparation and exploration

## 1.1 Creating Spark Session

**1. Create a SparkConf object for using as many local cores as possible, for a proper application name, and for changing the max partition byte configuration1 to enable a minimum of 2 partitions2 when reading each file in Spark SQL (so each dataframe should have at least 4 partitions when reading from the given datafiles). 2. Then create a SparkSession using the SparkConf object.**

```python
# Import needed libraries from pyspark
from pyspark import SparkConf

# Create pyspark configuration object
master = 'local[2]'
app_name = 'ass_2'
spark_conf = SparkConf().setMaster(master).setAppName(app_name)

# Create SparkSession
from pyspark import SparkConext
from pyspark.sql import SparkSession

spark = SparkSession.builder.config(conf = spark_conf).getOrCreate()
sc = spark.sparkContext
sc.setLogLevel('ERROR')
```

## 1.2 Loading the data

**1. Load each activity data into a Spark dataframe and cache the data. Then print out the row count of each dataframe.**

	* **In order to speed up the loading process, please specify the schema before reading the data into dataframes. You may find relevant schema info from the metadata file, however, note that some data may not fully comply with the schema. For those that do not comply with the schema, import them as StringType and further transform them in step 1.2.2.**

```python
# 1.2.1 Loading each activity
df_memory = spark.read.format("csv")\
            .option("header",True)\
            .option("inferSchema", True)\
            .option("escape",'"')\
            .load('./*memory*')

df_memory.printSchema()
```

![截屏2021-01-18 下午8.18.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-18 下午8.18.01.png)

```python
df_process = spark.read.format("csv")\
            .option("header", True)\
            .option("inferSchema", True)\
            .option("escape", '"')\
            .load("./*process*")
df_process.printSchema()
```

![截屏2021-01-18 下午8.18.42](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-18 下午8.18.42.png)

**1.For each column in each dataframe above,**

- **Check the null data (if any) and print out the corresponding count in each column**
- **Are these columns 'MINFLT', 'MAJFLT', 'VSTEXT', 'RSIZE', 'VGROW', 'RGROW' in memory data following the datatype from the metadata file? If not, please transform them into the proper formats**



### To find problem

```python
problem_cols = ['MINFLT', 'MAJFLT', 'VSTEXT', 'RSIZE', 'VGROW', 'RGROW']
df_select = df_memory.select(problem_cols)
```

 * 1)

   ```python
   # To check problem
   for i in df_select.take(df_select.count()):
       for j in i:
           try:
               if int(j):
                   break
           except:
               print(j)
   ```

* 2)

  ```python
  df_temp = df_memory.select('VGROW')
  p = []
  for i in df_temp.take(df_temp.count()):
      for j in i:
          try:
              if float(j) is True:
                  break
          except:
              if  (j[-1] != 'M') &  (j[-1] != 'K'):
                  p.append(j)
  ```

After finding,

```python
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType
#@udf(StringType())

def convert_format(data):
    if data[-1] == 'M':
        data = int(float(data[:-1]) * 1000000)
    elif data[-1] == 'K':
        data = int(float(data[:-1]) * 1000)
    else:
        data = data
    return data
convert_format_udf = udf(convert_format, StringType())
```

```python
## Some values have blank which need to be replaced
from pyspark.sql.functions import regexp_replace
from pyspark.sql.types import DoubleType

for col in problem_cols:
    df_memory = df_memory.withColumn(col, regexp_replace(col, ' ', ''))
    df_memory = df_memory.withColumn(col, convert_format_udf(col).cast(DoubleType()))
```

```python
from pyspark.sql.functions import when, isnan, count, col
df_memory.select([count(when(isnan(x) | col(x).isNull(),x)).alias(x) for x in df_memory.columns]).show()
```

![截屏2021-01-18 下午8.23.46](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-18 下午8.23.46.png)

```python
df_process.select([count(when(isnan(x) | col(x).isNull(),x)).alias(x) for x in df_process.columns]).show()
```

```python
df_memory.cache()
df_process.cache()
```



## 1.3 Exploring the data

**1. Show the count of attack and non-attack in each activity based on the column “attack”, then show the count of each kind of attack in process activity based on the column “type”.**

	* **Do you see any class imbalance? Examine and describe what you observe**

```python
# In memory
df_memory.groupby('attack').count().show()
```

![截屏2021-01-18 下午8.29.16](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-18 下午8.29.16.png)

```python
# In process
df_process.groupby('attack').count().show()
```

![截屏2021-01-18 下午8.29.37](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-18 下午8.29.37.png)

```python
# show the count of each kind of attack in process activity based on the column “type”.
df_process.groupby('attack','Type').count().show()
```

![截屏2021-01-18 下午8.30.35](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-18 下午8.30.35.png)

**2. For each numeric feature in each activity, show the basic statistics (including count,
mean, stddev, min, max); for each non-numeric feature in each activity, display the
top-10 values and the corresponding counts.**

   * **No need to show the labels at “attack” or “type” column**

```python
from pyspark.sql.types import IntegerType
#from pyspark.sql.types import dataType
numeric_memory_list = []
non_numeric_memory_list = []

numeric_process_list = []
non_numeric_process_list = []

for col in df_process.schema:
    if isinstance(col.dataType, IntegerType) | isinstance(col.dataType, DoubleType):
        numeric_process_list.append(col.name)
    else:
        non_numeric_process_list.append(col.name)

for col in df_memory.schema:
    if isinstance(col.dataType, IntegerType) | isinstance(col.dataType, DoubleType):
        numeric_memory_list.append(col.name)
    else:
        non_numeric_memory_list.append(col.name)
```

```python
## for each non-numeric feature in each activity, display the top-10 values and the corresponding counts
df_non_num_memory = df_memory.groupby(non_numeric_memory_list).count()
df_non_num_memory.sort('count', ascending = False).show(10)
```

3. **For each activity, present two plots3 worthy of presenting to the StopHacking
   company, describe your plots and discuss the findings from the plots**

   * **Hint - 1: you can use the basic plots (e.g. histograms, line charts, scatter
plots) for relationship between a column and the “attack” label (such as “ts”
and “attack”, “PID” and “attack”); or more advanced plots like correlation plots
for relationship between each column; 2: if your data is too large for the
plotting, consider using sampling before plotting**
   * **100 words max for each plot’s description and discussion**



# 2. Feature extraction and ML training

## 2.1 Preparing the training data and testing data

**1. Randomly split the dataset into 80% training data and 20% testing data for each use case**

```python
Mtrain, Mtest = df_memory.randomSplit([0.8,0.2])
Ptrain, Ptest = df_process.randomSplit([0.8,0.2])
```

**2. With the class imbalance observed from 1.3.1, for the binary classification use case 1 & 2, prepare rebalanced training data, with attack events and non-attack events being 1:2 ratio, while using 20% attack events data from the training data from 2.1.1. Cache the rebalanced training data, and display the count of each event's data.**

	* **Hint - you can use undersampling to get the rebalanced training data**

```python
## For Memory
from pyspark.sql.functions import count, col
## 20% attack events data from the training data from 2.1.1
Mtrain_attack = Mtrain.filter(col("attack") == 1).sample(0.2)
count_Mtrain_attack = Mtrain_attack.count()
#count_Mtrain_attack

Mtrain_normal = Mtrain.filter(col("attack") == 0)
## attack events and non-attack events being 1:2 ratio
Mtrain_normal = Mtrain_normal.sample(count_Mtrain_attack * 2 / Mtrain_normal.count())
count_Mtrain_normal = Mtrain_normal.count()
#count_Mtrain_normal
## Cache the rebalanced training data
Mtrain = Mtrain_normal.union(Mtrain_attack).cache()
```

```python
## For Process
from pyspark.sql.functions import count, col
## 20% attack events data from the training data from 2.1.1
Ptrain_attack = Ptrain.filter(col("attack") == 1).sample(0.2)
count_Ptrain_attack = Ptrain_attack.count()
#count_Mtrain_attack

Ptrain_normal = Ptrain.filter(col("attack") == 0)
## attack events and non-attack events being 1:2 ratio
Ptrain_normal = Ptrain_normal.sample(count_Ptrain_attack * 2 / Ptrain_normal.count())
count_Ptrain_normal = Ptrain_normal.count()
#count_Ptrain_normal
## Cache the rebalanced training data
Ptrain = Ptrain_normal.union(Ptrain_attack).cache()
```



## 2.2 Preparing features, labels and models

**1. Based on data exploration from 1.3.3, which features would you select? Discuss the reason for selecting them and how you plan to further transform them.**

	* **400 words max for the discussion**
	* **Hint - things to consider include whether to scale the numeric data, whether to choose one-hot encoding or string-indexing for a specific model**





















































