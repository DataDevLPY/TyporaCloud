

## Activity: Machine Learning with Spark (Transformer, Estimator and Pipeline API)

```python
# Import libraries from needed spark
from pyspark import SparkConf

# Create spark configuration object
master = 'local[*]'
app_name = 'transformer_estimator_pipeline'
spark_conf = SparkConf().setMaster(master).setAppName(app_name)

# Create SparkSession
from pyspark import SparkContext
from pyspark.sql import SparkSession

spark = SparkSession.builder.config(conf = spark_conf).getOrCreate()
sc = spark.sparkContext
sc.setLogLevel('ERROR')
```



```python
df_adult = spark.read.format('csv')\
		.option('header', True).option('escape', '"')\
  	.load('adult.csv')
```



```python
# To select features
cols= ['workclass', 'education', 'maritalstatus', 'occupation', 'relationship', 'race', 'gender', 'income']
df = df_adult[cols]
```



```python
# Checking Missing/Null values
from pyspark.sql.functions import when, count, isnan, col
df.select([count(when(isnan(c) | col(c).isNull(), c)).alias(c) for c in df.columns])
```

![截屏2021-01-08 下午1.53.13](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-08 下午1.53.13.png)



## Estimators, Transformers and Pipelines
<hr/>
Spark's machine learning library has three main abstractions.
<ol>
    <li><strong>Transformer:</strong> Takes dataframe as input and returns a new DataFrame with one or more columns appended to it. Implements a <code>.transform()</code> method.</li>
    <li><strong>Estimator:</strong> Takes dataframe as input and returns a model. Estimator learns from the data. It implements a <code>.fit()</code> method.</li>
    <li><strong>Pipeline:</strong> <strong>Combines</strong> together <code>transformers</code> and <code>estimators</code>. Pipelines implement a <code>.fit</code> method.</li>
</ol>

<div style="background:rgba(0,109,174,0.2);padding:10px;border-radius:4px"><strong style="color:#006DAE">NOTE: </strong>
    Spark appends columns to pre-existing <strong>immutable</strong> DataFrames rather than performing operations <strong>in-place</strong>.
</div>



### StopWordsRemover 

`StopWordsRemover` takes input as a sequence of strings and drops all stop words. Stop Words are the words that should be excluded from the input, because they appear frequently and don't carry much meaning.

```python
from pyspark.ml.feature import StopWordsRemover

sentenceData = spark.createDataFrame([
  	(0, ["I", "saw", "the", "red", "balloon"]),
    (1, ["Mary", "had", "a", "little", "lamb"])
], ['id','raw'])

remover = StopWordsRemover(inputCol = 'raw', outputCol = 'filtered')
remover.transform(sentenceData).show(truncate = False)
```

![截屏2021-01-08 下午2.04.14](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-08 下午2.04.14.png)



### StringIndexer 

<code>StringIndexer</code> encodes string columns as indices, It assigns a unique value to each category. We need to define the input column/ columns name and the output column/ columns name in which we want the results.

```python
from pyspark.ml.feature import StringIndexer

inputCols = [x for x in df.columns]
outputCols = [f'{x}_index' for x in df.columns]
indexer = StringIndexer(inputCols = inputCols, outputCols = outputCols)
df_indexed = indexer.fit(df).transform(df)

#TODO Display the output, only the output columns
df_indexed.select([col(x) for x in outputCols]).toPandas().head()
```

![截屏2021-01-08 下午8.52.48](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-08 下午8.52.48.png)

### One Hot Encoder (OHE) 
One hot encoding is representation of categorical variables as binary vectors. It works in 2 steps:
1. The categorical variables are mapped as integer values
2. Each integer value is represented as binary vector

```python
from pyspark.ml,feature import OneHotEncoder

#the outputcols of previous step act as input cols for this step
inputCols_OHE = [x for x in outputCols if x != 'income_index']
outputCols_OHE = [f'{x}_vec' for x in inputCols if x!='income']

#Define OneHotEncoder with the appropriate columns
encoder = OneHotEncoder(inputCols = inputCols_OHE, outputCols = outputCols_OHE)
# Call fit and transform to get the encoded results
df_encoded = encoder.fit(df_indexed).transform(df_indexed)
```

#### Rename the target column to label

`label` is popularly used as the name for the target variable. In supervised learning, we have a **labelled** dataset which is why the column name **label** makes sense.

```python
df_encoded=df_encoded.withColumnRenamed('income_index', 'label')
```



### VectorAssembler 

Finally, once we have transformed the data, we want to combine all the features into a single feature column to train the machine learning model. `VectorAssembler` combines the given list of columns to a *single vector* column.

```python
from pyspark.ml.feature import VectorAssembler

inputCols_VA= [x for x in outputCols_OHE]

#Define the assembler with appropriate input and output columns
assembler = VectorAssembler(inputCols = inputCols_VA, outputCol = 'feature')
#use the asseembler transform() to get encoded results
df_final = assembler.transform(df_encoded)
#Display the output
df_final.select('feature','label').show(5,truncate = False)
```

![截屏2021-01-08 下午9.57.36](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-08 下午9.57.36.png)

### ML Algorithm and Prediction 

Here we are using Logistic Regression for the classification. We will explore the details about this algorithm in the next tutorials. 

```python
# Splitting the data into testing and training set 90% into training and 10% for testing
train, test = df_final.randomSplit([0.9,0.1])

from pyspark.ml.classification import LogisticRegression
# Create a LogisticRegression instance. This instance is an Estimator.
lr = LogisticRegression(featuresCol='features',labelCol='label')
model = lr.fit(train)

#Here we use the model trained with the training data to give predictions for our test data
predicted_data = model.transform(test)
predicted_data.select('features','label','prediction').filter(predicted_data.label==1).show()
```

![截屏2021-01-08 下午10.03.37](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-08 下午10.03.37.png)

```python
#This gives the accuracy of the model we have built, 
trainingSummary = model.summary
trainingSummary.accuracy
```



```python
# splitting the data as training and test data.
train, test = df.randomSplit([0.9, 0.1], seed=12345)

# Stage 1: string indexer
from pyspark.ml.feature import StringIndexer
inputCols=[x for x in df.columns]
outputCols=['workclass_index','education_index','marital-status_index',\
            'occupation_index','relationship_index','race_index','gender_index','label']
stage_1 = StringIndexer(inputCols=inputCols, outputCols=outputCols)

# Stage 2: One Hot Encoder
from pyspark.ml.feature import OneHotEncoder
inputCols_OHE = [x for x in outputCols if x != 'label']
outputCols_OHE = [f'{x}_vec' for x in df.columns if x != 'income']

stage_2 = OneHotEncoder(inputCols = inputCols_OHE, outputCols = outputCols_OHE)

# Stage 3: Vector Assembler
from pyspark.ml.feature import VectorAssembler

inputCols_VA= [x for x in outputCols_OHE]
stage_3 = VectorAssembler(inputCols = inputCols_VA, outputCol = 'features')

# Stage 4: ML Algorithm
from pyspark.ml.classification import LogisticRegression

stage_4 = LogisticRegression(featuresCol = 'features', labelCol = 'label')

# Pipeline

from pyspark.ml import Pipeline
#plug the stages into the pipline
pipeline = Pipeline(stages = [stage_1,stage_2,stage_3,stage_4])
#train the model
model = pipeline.fit(train)
#generate the predictions
prediction = model.transform(test)
selected = prediction.select('features','label','probability','prediction')
selected.show(5, truncate = False)
```

![截屏2021-01-08 下午11.10.03](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-08 下午11.10.03.png)



















































