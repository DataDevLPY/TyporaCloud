# Part A : Working with RDDs and DataFrames

## 1. Working with RDD

### 1.1 Data Preparation and Loading

- 1). Write the code to create a ParkContext object using SparkSession, which tells Spark how to access a cluster .To create a SparkSession you first need to build a SparkConf object that contains information about your application. Give an appropriate name for your application and run Spark **locally** with **as many working processors as logical cores on tour machine.**
- 2). Import all the "Units" csv files from 2015-2019 into a single RDD.
- 3). Import all the "Crashes" csv files from 2015-2019 into a single RDD.
- 4). For each Units and Crashes RDDs, **remove the header rows and display the total count and first 10 records.**

```python
from pyspark import SparkConf

master = 'local[*]'
app_name = 'Assignment_1'
spark_conf = SparkConf().setMaster(master).setAppName(app_name)

from pyspark import SparkContext
from pyspark.sql import SparkSession

spark = SparkSession.builder.config(conf = spark_conf).getOrCreate()
sc = spark.sparkContext
sc.setLogLevel("ERROR")
```



```python
## Import all the "Units" csv files from 2015-2019 into a single RDD.
rdd_units = sc.textFile('./*Units.csv')
#rdd_units.count()
## Import all the “Crashes” csv files from 2015-2019 into a single RDD.
rdd_crashes = sc.textFile('./*Crash.csv')
#rdd_crashes.count()
```



```python
## For each Units and Crashes RDDs, 
## remove the header rows and 
## display the total count and first 10 records.

rdd_units_list = rdd_units.map(lambda line: [i.replace('"','') for i in line.split(',')])
#units_header_list.take(2)
units_header = rdd_units_list.first()
#units_header
rdd_units_list_re_header = rdd_units_list.filter(lambda row: row != units_header)

print(f'The count of units.csv: {rdd_units_list_re_header.count()}')
#rdd_units_list_re_header.take(10)

#################################
rdd_crash_list = rdd_crashes.map(lambda line: [i.replace('"','') for i in line.split(',')])
crash_header = rdd_crash_list.first()
rdd_crash_list_rm_header = rdd_crash_list.filter(lambda row: row != crash_header)
print(f'The count of crashes: {rdd_crash_list_rm_header.count()}')
#rdd_crash_list_rm_header.take(10)
```

![截屏2020-12-24 下午5.34.45](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-24 下午5.34.45.png)

### 1.2 Data Partitioning in RDD
* 1). How many partitions do the above RDDs have? How is the data in these RDDs
  partitioned by default, when we do not explicitly specify any partitioning strategy?
* 2). In the “Units” csv dataset, there is a column called Lic State which shows the state
  where the vehicle is registered. Assume we want to keep all the data related to SA in
    one partition and the rest of the data in another partition.
   * a. Create a Key Value Pair RDD with Lic State as the key and rest of the other columns as value.

   * b. Write the code to implement this partitioning in RDD using appropriate partitioning functions. 

   * c. Write the code to print the number of records in each partition. What does it tell about the data skewness?

```python
print('Default partitions for units: ',rdd_units.getNumPartitions())
print('Default partitions for crashes: ',rdd_crashes.getNumPartitions())
```



```python
# To get the Lic State Index
Lic_index = units_header.index("Lic State")
#print(f"{Lic_index}")

# To separate the key and value
def Lic_key_value(text):
    temp_text = text
    key = temp_text.pop(Lic_index)
    value = temp_text
    return [key,value]
    
unit_key_value_pair_list = rdd_units_list_re_header.map(Lic_key_value)
unit_key_value_pair_list.take(1)
```

![截屏2020-12-25 上午1.05.25](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-25 上午1.05.25.png)

```python
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
```

```python
def if_Lic_is_SA(key):
    partition = 0 
    if key != 'SA':
        partition = 1
    return partition

units_partition = unit_key_value_pair_list.partitionBy(2,if_Lic_is_SA)
print_partitions(units_partition)
```

![截屏2020-12-25 上午1.08.31](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-25 上午1.08.31.png)



### 1.3 Query/Analysis

For the Units RDD, write relevant RDD operations to answer the following queries.
* 1). Find the average age of male and female drivers separately.
* 2). What is the oldest and the newest vehicle year involved in the accident? Display the Registration State, Year and Unit type of the vehicle.

```python
# Find the average age of male and female drivers separately.
sex_index = units_header.index("Sex")
#print(sex_index)
age_index = units_header.index("Age")
#print(age_index)


# To set key value
def sex_age(text):
    temp_text = text
    key = text[sex_index]
    value = int(text[age_index])
    return [key, value]


# To check the age whether is available
def age_available(text):
    try:
        int(text[age_index])
        return True
    except:
        return False            
```



```python
rdd_male_list = rdd_units_list_re_header.filter(lambda line: line[sex_index] == 'Male')
rdd_male_list = rdd_male_list.filter(age_available)

rdd_female_list = rdd_units_list_re_header.filter(lambda line: line[sex_index] == 'Female')
rdd_female_list = rdd_female_list.filter(age_available)


male_count = rdd_male_list.count()
female_count = rdd_female_list.count()

male_sum_age = rdd_male_list.map(sex_age).reduceByKey(lambda sum,next: sum+next).collect()
female_sum_age = rdd_female_list.map(sex_age).reduceByKey(lambda sum,next: sum+next).collect()


average_male_age = round(male_sum_age[0][1]/male_count, 2)
average_female_age = round(female_sum_age[0][1]/female_count,2)
print('The average age of male is: ', average_male_age)
print('The average age of female is: ', average_female_age)
```

![截屏2020-12-25 下午5.36.31](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-25 下午5.36.31.png)

```python
# What is the oldest and the newest vehicle year involved in the accident? 
# Display the Registration State, Year and Unit type of the vehicle.
re_state_index = units_header.index('Veh Reg State')
year_index = units_header.index('Veh Year')
unit_type_index = units_header.index('Unit Type')


# To extract information
def extract_info(text):
    temp_text = text
    re_state = temp_text[re_state_index]
    year = int(temp_text[year_index])
    unit_type = temp_text[unit_type_index]
    return [re_state,[year, unit_type]]

# To check the year whether is available
def veh_year_available(text):
    try:
        int(text[year_index])
        return True
    except:
        return False


accident_veh_info_list = rdd_units_list_re_header.filter(veh_year_available)
after_extract_list = accident_veh_info_list.map(extract_info)


max_year = after_extract_list.max(key = lambda text: text[1][0])
min_year = after_extract_list.min(key = lambda text: text[1][0])
print(f'The newest vehicle year involved in the accident: {max_year}')
print(f'The oldest vehicle year involved in the accident: {min_year}')
```

![截屏2020-12-25 下午6.33.45](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-25 下午6.33.45.png)



## 2. Working with DataFrames 
In this section, you will need to load the given datasets into PySpark DataFrames and use
DataFrame functions to answer the queries.

### 2.1 Data Preparation and Loading
* 1). Load all units and crash data into two separate dataframes.
* 2). Display the schema of the final two dataframes.

```python
df_units = spark.read.format('csv').option('header', True).option('escape', '"')\
    .load('./*Units.csv')

df_crash = spark.read.format('csv').option('header', True).option('escape', '"')\
    .load('./*Crash.csv')
  
  
df_units.printSchema()
df_crash.printSchema()
```

![截屏2020-12-27 上午12.12.10](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-27 上午12.12.10.png)



### 2.2 Query/Analysis 
Implement the following queries using dataframes. You need to be able to perform operations like filtering, sorting, joining and group by using the functions provided by the DataFrame API.
* 1). Find all the crash events in Adelaide where the total number of casualties in the event is more than 3.
* 2). Display 10 crash events with highest casualties .
* 3). Find the total number of fatalities for each crash type.
* 4). Find the total number of casualties for each suburb when the vehicle was driven by an unlicensed driver. You are required to display the name of the suburb and the total number of casualties.

```python
from pyspark.sql.functions import col
from pyspark.sql.types import IntegerType

# Find all the crash events in Adelaide 
# where the total number of casualties in the event is more than 3.
df_crash = df_crash.withColumn('Total Cas', col('Total Cas').cast(IntegerType()))
#df_crash.printSchema()
adelaide_crash_num = df_crash.filter((col('Suburb'.upper()) == 'ADELAIDE')\
                                     & (col('Total Cas') > 3)
                                    )
adelaide_crash_num.count()
```



```python
# Display 10 crash events with highest casualties .
Top_ten_crash = df_crash.sort('Total Cas', ascending = False)
Top_ten_crash.select('Total Cas').take(10)
```

![截屏2020-12-27 下午3.12.36](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-27 下午3.12.36.png)

```python
# Find the total number of fatalities for each crash type.
from pyspark.sql.functions import count,sum
df_crash = df_crash.withColumn('Total Fats', col('Total Fats').cast(IntegerType()))
#df_crash.printSchema()
fats_for_type_num = df_crash.groupby('Crash Type').agg(sum('Total Fats'))
fats_for_type_num.show()
```

![截屏2020-12-27 下午3.53.32](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-27 下午3.53.32.png)

```python
# Find the total number of casualties for each suburb 
# when the vehicle was driven by an unlicensed driver. 
# You are required to display the name of the suburb and the total number of casualties.
unit_crash_join = df_units.join(df_crash, df_units['REPORT_ID'] == df_crash['REPORT_ID'])
unlicense_event = unit_crash_join.filter(col('Licence Type') == 'Unlicenced')
#unlicense_event.select('Licence Type').take(10)
cas_for_suburb = unlicense_event.groupby('Suburb').agg(sum('Total Cas'))
cas_for_suburb.show()
```

![截屏2020-12-27 下午6.44.54](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-27 下午6.44.54.png)

### 2.3 Severity Analysis
In this section, we want to analyze whether severity of accidents is higher when the driver is on drugs or alcohol compared to when the driver is normal. The severity of the crash is given by the column “CSEF Severity”, the three levels of severity is given below (also included in the Metadata file). Similarly the columns “DUI Involved” and “Drugs Involved” tell whether the driver has been detected with blood alcohol and drugs respectively.


Using the DataFrame for crash events, implement the following queries:
* 1). Find the total number of crash events for each severity level. Which severity level is the
  most common?
* 2). Compute the total number of crash events for each severity level and the percentage for the four different scenarios.
   * A sample output for each of these scenarios is given below.
        * a. When the driver is tested positive on drugs.
        * b. When the driver is tested positive for blood alcohol concentration.
        * c. When the driver is tested positive for both drugs and blood alcohol
        * d. When the driver is tested negative for both (no alcohol and no drugs). 

Compare the results in these 4 scenarios. Briefly explain the observation from this analysis.

```python
# Find the total number of crash events for each severity level
severity_count = df_crash.groupby('CSEF Severity').count()\
								.sort('count', ascending = False)
severity_count.show()
```

![截屏2020-12-27 下午7.27.17](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-27 下午7.27.17.png)

```python
# Compute the total number of crash events for each severity level 
# and the percentage for When the driver is tested positive on drugs.

drug_positive = df_crash.filter(col('Drugs Involved') == 'Y')
drug_positive_count = drug_positive.count()
severity_by_drug = drug_positive.groupby('CSEF Severity')\
                .agg(count('CSEF Severity').alias('drug_count'))
#severity_by_drug.show()

final_percent_a = severity_by_drug.join(severity_count, \
                    severity_count['CSEF Severity'] == severity_by_drug['CSEF Severity'])
final_percent_a = final_percent_a.drop(severity_count['CSEF Severity'])

## First Method
final_percent_a = final_percent_a.withColumn('drug_count',
                                             col('drug_count')/col('count'))
final_percent_a = final_percent_a.withColumnRenamed('drug_count', 'Percentage')
final_percent_a.select('CSEF Severity','count','Percentage').show()

## Second Method
## Define a function to compute the real percentage
#from pyspark.sql.functions import udf
#def get_percentage(x,y):
#    output = str((x/y)*100) + '%'
#    return output

## Create a udf to use get_percantage()
#percentage_col = udf(get_percentage)
#final_percent_a.select('CSEF Severity','count',
#    percentage_col(final_percent_a['drug_count'], final_percent_a['count']))

```

![截屏2020-12-27 下午8.22.56](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-27 下午8.22.56.png)

```python
# Compute the total number of crash events for each severity level 
# and the percentage for When the driver is tested positive for blood alcohol concentration.

dui_positive = df_crash.filter(col('DUI Involved') == 'Y')
dui_positive_count = dui_positive.count()
severity_by_dui = dui_positive.groupby('CSEF Severity')\
                .agg(count('CSEF Severity').alias('dui_count'))
#severity_by_dui.show()

final_percent_a = severity_by_dui.join(severity_count, \
                        severity_count['CSEF Severity'] == severity_by_dui['CSEF Severity'])
final_percent_a = final_percent_a.drop(severity_count['CSEF Severity'])


final_percent_a = final_percent_a.withColumn('dui_count', col('dui_count')/col('count'))
final_percent_a = final_percent_a.withColumnRenamed('dui_count', 'Percentage')
final_percent_a.select('CSEF Severity','count','Percentage').show()
```

![截屏2020-12-27 下午11.09.05](/Users/peiyang/Library/Application Support/typora-user-images/截屏2020-12-27 下午11.09.05.png)





























