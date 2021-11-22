

# RDD (Resilient Distributed Datasets)

## 1. SparkContext and SparkSession

Apache Spark community released a powerful Python package, **`pyspark`**.  Using **`pyspark`**, we can initialise Spark, create RDD from the data, sort, filter and sample the data. Especially, we will use and import **`SparkContext`** from **`pyspark`**, which is the main entry point for Spark Core functionality. The **`SparkSession`** object provides methods used to create DataFrames from various input sources.

Spark applications run as independent sets of processes on a cluster, which is specified by the **`SparkContext`** object. **`SparkContext`** can connect to several types of cluster managers (local (standalone), Mesos or YARN), which allocate resources across applications. Once connected, Spark acquires executors on nodes in the cluster, which are processes that run computations and store data for your application. Next, it sends your application code (passed to `SparkContext`) to the executors. Finally, **`SparkContext`** sends tasks to the executors to run.

```python
from pyspark import SparkConf
## Create Spark Configuration Object
# Local[*]: run Spark in local mode with as many working processors as logical cores on your machine 
# If we want Spark to run locally with 'k' worker threads, we can specify as "local[k]".
master = "local[*]"
# appName field is a name to be shown on the Spark cluster UI page
app_name = "Assignment_1"
# Setup configuration parameters for Spark
spark_conf = SparkConf().setMaster(master).setAppName(app_name)

## Import SparkContext and Spark Session classes
from pyspark import SparkContext #Spark
from pyspark.sql import SparkSession #Spark SQL
# Using SparkSession
spark = SparkSession.builder.config(conf = spark_conf).getOrCreate()
sc = spark.sparkContext
sc.setLogLevel('ERROR')
```



## 2. RDD object

Resilient Distributed Dataset (RDD) is Spark's core abstraction for working with data. It is simply an immutable distributed collection of objects. Spark automatically distributed the data contained in RDDs across the cluster and parallelizes the operations performed on themEach RDD is split into multiple partitions, which may be computed on different nodes of the cluster. RDDs can contain any type of Python, Java, or Scala objects, including user defined classed. In Spark, all work is expressed as either creating new RDDs, transforming existing RDDs, or calling operations on RDDs to compute a result.

The class `pyspark.SparkContext` creates a client which connects to a Spark cluster. This client can be used to create an RDD object. There are two methods from this class for directly creating RDD objects:

- `parallelize()`
- `textFile()`

`parallelize()` distribute a local **python collection** to form an RDD. Common built-in python collections include `list`, `tuple` or `set`.

```python
## FROM A LIST

# By default the number of partitions will be the number of threads
data_list = [i for i in range(10)]
rdd = sc.parallelize(data_list)

print('Default partitions: ',rdd.getNumPartitions())

# the function parallelize can have a second argument to indicate manually how many
# partitions for the data
rdd = sc.parallelize(data_list,5)

print('Manual partitions: ',rdd.getNumPartitions())

# Show the data by performing the action *collect*
rdd.collect()
```



## 3. File Read

The `textFile()` function reads a text file and returns it as an **RDD of strings**. Usually, you will need to apply some **map** functions to transform each elements of the RDD to some data structure/type that is suitable for data analysis.

**When using `textFile()`, each line of the text file becomes an element in the resulting RDD.**

```python
## Load the file
# /*file_name
file_rdd = sc.textFile("file_name")

# Check the count number
file_rdd.count()

## split each value
## map is to cycle each line by same method
file_rdd_list = file_rdd.map(lambda line: [i.replace('"','') for i in line.split(',')])
## To get the header
header = file_rdd_list.first()
## Remove header / It is a list
file_rdd_list_rmH = file_rdd_list.filter(lambda row: row != header)
```

### File information

```python
print(f"Total partitions: {file_rdd_list_rmH.getNumPartitions()}")
print(f"Number of lines: {file_rdd_list_rmH.count()}")
```



## 4. RDD Operations

RDDs support two types of operations:

- transformations and
- actions.

*Transformations* are operations on RDDs that return a new RDD, such as `map()` and `filter()`.

*Actions* are operations that return a result to the driver program or write it to storage, and kick off a computation, such as `count()` and `first()`.

Spark treats transformations and actions very differently:

* transformations return RDDs
* actions return some other data type.

### Element-wise transformations

These functions are probably the most commonly used functions when dealing with an RDD object.

- `map()`

- `mapValues()`

- `flatMap()`

- `flatMapValues()`

  

  **map()**

  The `map()` method applies a function to each elements of the RDD. Each element has to be a valid input to the function. The returned RDD has the function outputs as its new elements.

  Elements in the RDD object `map_exp_rdd` below are rows of the [mtcars](http://localhost:8967/notebooks/Desktop/FIT5202 Data Processing for Big Data/week 1/Tutorial/mtcars.csv) in string format. We are going to apply the `map()` function multiple times to convert each string elements as a list elements. Each list element has two values: the first value will be the auto model in string format; the second value will be a list of numeric values.

  ​	Example:

  ```python
  # create an example RDD
  map_exp_rdd = sc.textFile('mtcars.csv')
  print('Default partitions: ',map_exp_rdd.getNumPartitions())
  # show 4 records with the Spark *action* take
  map_exp_rdd.take(4)
  ```

  ![截屏2020-12-12 下午4.00.20](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220110981.png?token=AWS37JJQW6U27PFHBWBEO4LBTJ644)

  

  ```python
  # split auto model from other feature values
  map_exp_rdd_1 = map_exp_rdd.map(lambda x: x.split(',')).map(lambda x: (x[0], x[1:]))
  map_exp_rdd_1.take(4)
  ```

  ![截屏2020-12-12 下午4.01.00](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220110225.png?token=AWS37JJ4X5V4QGKMYIA7YETBTJ65A)

  

  ```python
  # remove the header row
  header = map_exp_rdd.first()
  # the filter method apply a function to each elemnts. The function output is a boolean value (TRUE or FALSE)
  # elements that have output TRUE will be kept.
  map_exp_rdd_1 = map_exp_rdd.filter(lambda x: x != header)
  # show 4 records with the Spark *action* take
  map_exp_rdd_1.take(4)
  ```

  ![截屏2020-12-12 下午4.01.38](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220110308.png?token=AWS37JITCG6PBIW5JBOJQF3BTJ65K)

  

  **Implement function with logic to be applied to the RDDs**

  ```python
  def parseRecord(line):
      # Split line separated by comma
      array_line = line.split(',')
      # Return a tuple with the car model as first element and the remaining as the second element
      return (array_line[0], array_line[1:])
    
  
  map_exp_rdd_2 = map_exp_rdd_1.map(parseRecord)
  map_exp_rdd_2.take(4)
  ```

  ![截屏2020-12-12 下午4.02.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220111183.png?token=AWS37JL4OS5MI254FIZ35XLBTJ66I)

  

  ```python
  # convert string values to numeric values
  map_exp_rdd_3 = map_exp_rdd_2.map(lambda x: (x[0], list(map(float, x[1]))))
  map_exp_rdd_3.take(4)
  ```

  ![截屏2020-12-12 下午4.02.53](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220111755.png?token=AWS37JPIFYH2TU6Q7W5FUUDBTJ66U)

​	**mapValues()**

​		The `mapValues` function requires that each element in the RDD has a **key/value** pair structure, for example, a 	tuple of 2 items, or a list of 2 items. The `mapValues` function applies a function to each of the element values. 	The element key will remain unchanged.

​		Example:

```python
mapValues_exp_rdd = map_exp_rdd_3
mapValues_exp_rdd.take(4)
```

![截屏2020-12-12 下午3.58.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220111997.png?token=AWS37JLRQWON2RF7G6AJZC3BTJ67A)

```python
# Uncomment the command below to install numpy
#!pip install numpy
import numpy as np
mapValues_exp_rdd_1 = mapValues_exp_rdd.mapValues(lambda x: np.mean(x))
mapValues_exp_rdd_1.take(4)
```

![截屏2020-12-12 下午4.18.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220111673.png?token=AWS37JKJH2LK35Z5WKAR43TBTJ67I)

When using mapValues(), the x in the above lambda function refers to the element value, not including the element key.

​	**`flatMap()`**

​	This function first applies a function to each elements of an RDD and then flatten the results. We can simply use 	this function to flatten elements of an RDD without extra operation on each elements.

```python
x = [('a', 'b', 'c'), ('a', 'a'), ('c', 'c', 'c', 'd')]
flatMap_exp_rdd = sc.parallelize(x)
flatMap_exp_rdd.collect()
```

```python
flatMap_exp_rdd_1 = flatMap_exp_rdd.flatMap(lambda x: x)
flatMap_exp_rdd_1.collect()
```

![截屏2020-12-12 下午4.22.45](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220111238.png?token=AWS37JJP3TY6ILEDB7LFST3BTJ67U)

​	**`flatMapValues()`**

​	The `flatMapValues` function requires that each element in the RDD has a **key/value** pair structure. It applies a 	function to each **element value** of the RDD object and then flatten the results.

| sample id |  A   |  B   |  C   |
| :-------: | :--: | :--: | :--: |
|     1     |  23  |  18  |  32  |
|     2     |  18  |  29  |  31  |
|     3     |  34  |  21  |  18  |

```python
# example data
my_data = [
    [1, (23, 28, 32)],
    [2, (18, 29, 31)],
    [3, (34, 21, 18)]
]
flatMapValues_exp_rdd = sc.parallelize(my_data)
flatMapValues_exp_rdd.collect()
```

![截屏2020-12-13 下午2.54.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220111018.png?token=AWS37JIIR4PF4RJOXZGE7JLBTJ7AC)

```python
# merge A,B,and C columns into on column and add the type column
flatMapValues_exp_rdd_1 = flatMapValues_exp_rdd.flatMapValues(lambda x: list(zip(list('ABC'), x)))
flatMapValues_exp_rdd_1.collect()
```

![截屏2020-12-13 下午2.54.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220111769.png?token=AWS37JILRI66IDDHSR5YMBTBTJ7A2)

```python
# unpack the element values
flatMapValues_exp_rdd_2 = flatMapValues_exp_rdd_1.map(lambda x: [x[0]] + list(x[1]) )
flatMapValues_exp_rdd_2.collect()
```

![截屏2020-12-13 下午2.59.59](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220111994.png?token=AWS37JNXVXH5QVA3MBMF3WTBTJ7BW)



## 5. Transform data to a tuple RDD

Key Value pairs (tuples) are a common data type required in many operations in Spark. They are commonly used to perform aggregations. Spark provides special operations on RDDs containing key value pairs, These RDDs are called pair RDDs. They expose operations that allow you to act on each key in parallel or regroup data across the network. For example, pair RDDs have a reduceByKey() method that can aggregate data separately for each key, and a join() method that can merge two RDDs together by grouping elements with the same key.

```python
iris_rdd = sc.textFile('iris.csv', use_unicode=True)
iris_rdd.take(2)
```

![截屏2020-12-13 下午3.08.17](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220112108.png?token=AWS37JNAM27CSUIKFDGPESLBTJ7CO)

```python
# We will use the data from iris.csv and convert it into a key value pair using map() function.
iris_rdd_2 = iris_rdd.map(lambda x: x.split(',')).\
    filter(lambda x: x[0] != 'sepal_length').\
    map(lambda x: (x[-1], [*map(float, x[:-1])]))
iris_rdd_2.take(5)
```

![截屏2020-12-13 下午3.13.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220112193.png?token=AWS37JJRONHB5JA7WHWYQUTBTJ7CS)

​	**Accumulator**

Accumulators provides a simple syntax for aggregating values from worker nodes back to the driver program. They are only “added” to through an associative and commutative operation and can therefore be efficiently supported in parallel.

​	Example:

```python
twitter_rdd = sc.textFile('twitter.txt', 3)
blank_lines = sc.accumulator(0) # Create Accumulator[int] intitialized to 0

def extract_blank_lines(line):
    global blank_lines # make the global variable accessible
    lll = {'a':1}
    if line == "":
        print(type(line))
        blank_lines += 1
    return line.split(" ")
        
word_rdds = twitter_rdd.flatMap(extract_blank_lines)
word_rdds.collect()

print("Blank lines: %d" %blank_lines.value)
```



```python
bad_words = {'crap', 'rascals', 'fuck'}
bad_words_count = sc.accumulator(0) # Create Accumulator[int] intitialized to 0

broadcast_bad_words = sc.broadcast(bad_words)

twitter_rdd = sc.textFile('twitter.txt', 3)
twitter_rdd.collect()

def check_bad_words(line):
    global bad_words_count # make the global variable accessible
    words = line.split(" ")
    for word in words:
        if word in broadcast_bad_words.value: # using the broadcast variable in the function that runds on the cluster
            bad_words_count += 1
            
filtered_tweets = twitter_rdd.filter(check_bad_words)
filtered_tweets.collect()

print("Bad words: %d" %bad_words_count.value)
```

## 6. RDD partitioning

```python
from pyspark.rdd import RDD
# A Function to print the data items in each RDD
# Same function is used to print out the data items of a Dataframe later.
def print_partitions(data):
  	if isinstance(data, RDD):
    		numPartitions = data.getNumPatitions()
    		partitions = data.glom().collect()
  	else:
  			numPartitions = data.rdd.getNumPartitions()
        partitions = data.rdd.glom().collect()
    
    print(f"####### NUMBER OF PARTITIONS: {numPartitions}")
    for index, partition in enumerate(partitions):
      	# show partition if it is not empty
        if len(partition) > 0:
          	print(f"Partition {index}: {len(partition)} records")
```

Sample data:

```python
# Sample data used for demonstrating the partitioning
list_tutors = [(1,'Aaditya'),(2,'Chinnavit'),(3,'Neha'),(4,'Huashun'),(5,'Mohammad'), (10,'Peter'),(11,'Paras'),(12, 'Tooba'),(3, 'David'),(18,'Cheng'),(9,'Haqqani')]

# Define the number of partitions
no_of_partitions = 4
```

Data partitioning is the fundamental step for parallel search algorithms as parallelism in query and search processing is achieved through data partionining. In this activity, we will consider the following **three** partitioning strategies:

#### 1). Round-robin data partitioning

Round-robin data partitioning is the simplest data partitioning method in which each record in turn is allocated to a processing element (simply processor). Since it distributes the data evenly among all processors, it is also known as "equal-partitioning".

```python
# random equal partition
rdd = sc.parallelize(list_tutors, no_of_partitions)

print("Number of partitions:{}".format(rdd.getNumPartitions()))
print("Partitioner:{}".format(rdd.partitioner))
print_partitions(rdd)  
```

​	Example:

```python
# Split each line to [key, value]
# 如果每组坑位均为2， 则0号坑自定义为key，1号坑则是value
def map_pair(field):
  	temp_field = field.copy()
    targart = temp_field.pop(8)
    rest_field = temp_field
    return (target, rest_field)
# 以key为目的分割
file_rdd_list_rmH_pairs = file_rdd_list_rmH.map(map_pair)


from pyspark.rdd import RDD
# A Function to print the data items in each RDD
# Same function is used to print out the data items of a DataFrame later
def print_partitions(data):
    if isinstance(data, RDD):
        numPartitions = data.getNumPartitions()
        partitions = data.glom().collect()
    else:
        numPartitions = data.rdd.getNumPartitions()
        partitions = data.rdd.glom().collect()
    
    print(f"####### NUMBER OF PARTITIONS: {numPartitions}")
    for index, partition in enumerate(partitions):
        # show partition if it is not empty
        if len(partition) > 0:
            print(f"Partition {index}: {len(partition)} records")


def partition_if_key():
  #主要看key的值有几种，假设2种
  partition = 0
  if key != 'which':
    	partition = 1
  return partition

file_pairs_paritions = file_rdd_list_rmH_pairs.partitionBy(2, partition_if_key)
print_partitions(file_pairs_paritions)
```

#### 2). Range data partitioning

Range data partitioning records based on a given range of the partitioning attribute. For example,the student table is partitioned based on "Last Name" based on the alphabetical order (i.e. A ~ Z).

```python
no_of_partitions=4

#Find the size of the elements in RDD
chunk_size = len(list_tutors)/no_of_partitions
#Define a range of values by key to distribute across partitions
#Here for simplicity, we are defining the range i.e. keys from 1-4 to fall in first partition, 5-9 in second partition and so on
range_arr=[[1,4],[5,9],[10,14],[15,19]]

def range_function(key):
    for index,item in enumerate(range_arr):
        if key >=item[0] and key <=item[1]:
            return index
            
# range partition
range_partitioned_rdd = rdd.partitionBy(no_of_partitions, range_function)
print_partitions(range_partitioned_rdd)
```

#### 3). Hash data partitioning

Hash data partitioning makes a partition based on a particular attribute using a hash function. The result of a hash function determines the processor where the record will be placed. Thus, all records within a partition have the same hash value.

```python
#Hash Function to implement Hash Partitioning 
#Just computes the sum of digits
#Example : hash_function(12) produces 3 i.e. 2 + 1
def hash_function(key):
    total = 0
    for digit in str(key):
        total += int(digit)
    return total
  
# hash partitioning
hash_partitioned_rdd = rdd.partitionBy(no_of_partitions, hash_function)
print_partitions(hash_partitioned_rdd) 
```



## Query/ Analysis

```python
######## For example #########
age_index = header.index['Age']
sex_index = header.index['Sex']

## clean dirty data
def age_available(input):
  try:
    	int(input[age_index])
      return True
  except:
    	return False
## combine key-value
def sex_age_pairs(input):
  	temp = input.copy()
    sex = temp[sex_index]
    age = int(temp[age_index])
    return (sex, age)

# To filter all of data about male and available data
file_rdd_male = file_rdd_list_rmH.filter(lambda line: line[sex_index] == 'male' ).\
		filter(age_available)
sum_age = file_rdd_male.map(sex_age_pairs).reduceByKey(lambda sum, next: sum+next).collect()
sum = sum_age[0][-1]

## .max
## .min
```



