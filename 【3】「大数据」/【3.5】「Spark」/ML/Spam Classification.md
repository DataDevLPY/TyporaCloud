# Lecture Demo Week 5

```python
# Import libraries needed from pyspark
from pyspark import SparkConf
# Create spark configuration object
master = 'local[*]'
app_name = 'Lecture Demo'
spark_conf = SparkConf().setMaster(master).setAppName(app_name)

# Create sparksession
from pyspark import SparkContext
from pyspark import SparkSession

spark = SparkSession.builder.config(conf = spark_conf).getOrCreate()
sc = spark.sparkContext
sc.setLogLevel('ERROR')
```

```python
# Read data
df = spark.read.csv("SMSSpamCollection", sep = "\t", inferSchema = True, header = False)
df.show(5, truncate = False)
```

![截屏2021-01-07 下午10.35.58](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-07 下午10.35.58.png)

```python
# Rename columns
df = df.withColumnRenamed('_c0', 'label').withColumnRenamed('_c1', 'message')
df.show(5, truncate = False)
```

![截屏2021-01-07 下午10.36.36](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-07 下午10.36.36.png)

```python
# Change the status column to numeric: ham to 1.0 and spam to 0. 
from pyspark.sql.functions import when 

df = df.withColumn('label', when(df['label'] == 'ham', 1.0).otherwise(0.0))
df.show(5, truncate = False)
```

![截屏2021-01-07 下午10.39.32](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-07 下午10.39.32.png)



### Feature Transformation: Tokenization

```python
# To Tokenize the messages
from pyspark.ml.feature import Tokenizer

tokenizer = Tokenizer(inputCol = 'message', outputCol = 'words')
wordsData = tokenizer.transform(df)
wordsData.show(5, truncate = False)
```

![截屏2021-01-07 下午10.43.52](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-07 下午10.43.52.png)

### Feature Extraction: CountVectorizer

CountVectorizer converts the list of tokens above to vectors of token counts.

```python
from pyspark.ml.feature import CountVectorizer
count = CountVectorizer(inputCol = 'words', outputCol = 'rawFeatures')
model = count.fit(wordsdata)
featurizedData = model.transfrom(wordsData)
featurizedData.toPandas()
```

![截屏2021-01-07 下午10.50.43](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-07 下午10.50.43.png)

```python
from pyspark.ml.feature import IDF
# IDF down-weighs features which appear frequently in a corpus. 
# This generally improves performance when using text as features since most frequent, 
# and hence less important words, get down-weighed.

idf = IDF(inputCol = 'rawFeatures', outputCol = 'features')
idfModel = idf.fit(featurizedData)
rescaledData = idfModel.transform(featurizedData)
rescaledData.select('label','features').show(5, truncate = False)
# We want only the label and features columns for our machine learning models
```

![截屏2021-01-07 下午11.02.43](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-07 下午11.02.43.png)

### ML Pipeline

```python
# Training Data
# Split data into training (80%) and testing (20%)
seed = 0 # set seed for reproducibility
trainDF, testDF = rescaledData.randomSplit([0.8,0.2],seed)

print("Number of training data: ", trainDF.count())
print("Number of test data: ", testDF.count())
```

![截屏2021-01-07 下午11.10.36](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-07 下午11.10.36.png)

## Model: Logistic Regression Classifier

Logistic regression is a popular method to predict a categorical response. It is a special case of Generalized Linear models that predicts the probability of the outcomes. In spark.ml logistic regression can be used to predict a binary outcome by using binomial logistic regression, or it can be used to predict a multiclass outcome by using multinomial logistic regression.

```python
# To build best model
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml.tuning import CrossValidor, ParamGridBuilder
import numpy as np

lr =LogisticRegression(maxIter = 10)

paramGrid_lr = ParamGridBuilder()\
		.addGrid(lr.regParam， np.linspace(0.3, 0.01, 10))\
  	.addGrid(lr.elasticNetParam, np.linspace(0.3, 0.8, 6))\
    .build()

crossval_lr = CrossValidator(estimator=lr,
                          estimatorParamMaps=paramGrid_lr,
                          evaluator=BinaryClassificationEvaluator(),
                          numFolds= 5)    

cvModel_lr = crossval_lr.fit(trainDF)
best_model_lr = cvModel_lr.bestModel.summary


```



```python
train_fit_lr = best_model_lr.predictions.select('label','prediction')
train_fit_lr.groupBy('label','prediction').count().show()
```



```python
# How accurate is the model? 
# we use MulticlassClassificationEvaluator for the accuracy of the model
# We can get the f1 score, accuracy, precision.
from pyspark.ml.evaluation import MulticlassClassificationEvaluator
my_mc_lr = MulticlassClassificationEvaluator(predictionCol='prediction', labelCol='label', metricName='accuracy')
my_mc_lr.evaluate(best_model_lr.predictions)
```



### Model Evaluation

```python
predictions_lr = cvModel_lr.transform(testDF)
predictions_lr.groupBy('label','prediction').count()
# How accurate is the model?
# we use MulticlassClassificationEvaluator for the accuracy of the model
my_mc_lr = MulticlassClassificationEvaluator(predictionCol='prediction', labelCol='label', metricName='accuracy')
my_mc_lr.evaluate(predictions_lr)
```





































