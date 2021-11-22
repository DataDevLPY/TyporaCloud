

# Machine Learning 

```python
# create entry points to spark
from pyspark import SparkContext # Spark
from pyspark.sql import SparkSession # Spark SQL

# We add this line to avoid an error : "Cannot run multiple SparkContexts at once". 
# If there is an existing spark context, we will reuse it instead of creating a new context.
sc = SparkContext.getOrCreate()

# local[*]: run Spark locally with as many working processors as logical cores on your machine.
# In the field of `master`, we use a local server with as many working processors (or threads) as possible (i.e. `local[*]`). 
# If we want Spark to run locally with 'k' worker threads, we can specify as `local[k]`.
# The `appName` field is a name to be shown on the Sparking cluster UI. 

# If there is no existing spark context, we now create a new context
if (sc is None):
    sc = SparkContext(master="local[*]", appName="Lecture Demo Week 05")
spark = SparkSession(sparkContext=sc)
```



```python
# Import libraries needed from pyspark
from pyspark import SparkConf
# Create spark configuration Object
master = "local[*]"
app_name = "transformer_estimator_pipeline"
spark_conf = SparkConf().setMaster(master).setAppName(app_name)

# Create SparkSession
from pyspark import SparkContext
from pyspark.sql import SparkSession

spark = SparkSession.builder.config(conf = spark_conf).getOrCreate()
sc = spark.sparkContext
sc.setLogLevel('ERROR')
```



## Data Checking

1. See some sample data, check the schema

2. Check the statistics of the numerical columns in the dataset

3. Target Variable Distribution, what are the number of instances the target variable has?

   ```python
   from pyspark.sql import functions as F
   df_deposit = df_bank.select(F.col('deposit')).distinct()
   ```

   

4. Check if the dataframe contains null values?

   ```python
   from pyspark.sql.functions import isnan, when, count, col
   df_bank.select([count(when(isnan(x) | col(x).isNull(), x)).alias(x) for x in df_bank.columns]).show()
   ```

   

5. Separate the numerical and non-numerical columns to apply feature engineering



## Estimators, Transformers

### StopRemover

```python
from pyspark.ml.feature import StopWordsRemover
sentenceData = spark.createDataFrame([
    (0, ["I", "saw", "the", "red", "balloon"]),
    (1, ["Mary", "had", "a", "little", "lamb"])
], ["id", "raw"])

remover = StopWordsRemover(inputCol="raw", outputCol="filtered")
remover.transform(sentenceData).show(truncate=False)
```





### StringIndexer

```python
from pyspark.ml.feature import StringIndexer
inputCols_index = ['workclass','education','marital-status','occupation','relationship','race','gender','income']
outputCols_index = ['workclass_index','education_index','marital-status_index','occupation_index','relationship_index','race_index','gender_index','label']

indexer = StringIndexer(inputCols = inputCols_index, outputCols = outputCols_index)
```



### One Hot Encoder(OHE)

```python
from pyspark.ml.feature import OneHotEncoder
inputCols_OHE = [x for x in outputCols_index if x != 'label']
outputCols_OHE = [f'{x}_vec' for x in inputCols_index if x != 'income']

encoder = OneHotEncoder(inputCols = inputCols_OHE, outputCols = outputCols_OHE)
```



### VectorAssembler

```python
from pyspark.ml.feature import VectorAssembler
inputCols_VA = [x for x in outputCols_OHE]

assembler = VectorAssembler(inputCols = inputCOls_VA, outputCol = 'feature')
```



## Classification

```python
(train, test) = df.randomSplit([0.8, 0.2])
```

### Pipeline API

```python
from pyspark.ml import Pipeline
```



### Decision Tree

```python
from pyspark.ml.classification import DecisionTreeClassifier
dt = DecisionTreeClassifier(featuresCol = 'feature', labelCol = 'label', maxDepth = 3)

dtPipeline = Pipeline(stages = [indexer,encoder,assembler,dt])
dtModel = dtPipeline.fit(train)

dtPredictions = dtModel.transform(test)
dtSelected = dtPredictions.select('features','label','probability','prediction').show(10)
```



### Random Forrest

```python
from pyspark.ml.classification import RandomForestClassifier

rf = RandomForestClassifier(labelCol = 'label', featuresCol = 'features', numTrees = 10)
rfPipeline = Pipeline(stages = [indexer,encoder,assembler,rf])
rfModel = rfPipeline.fit(train)

rfPredictions = rfModel.transform(test)
rfSelected = rfPredictions.select('features','label','probability','prediction').show(10)
```



### Logistic Regression

```python
from pyspark.ml.classification import LogisticRegression
lr = LogisticRegression(featuresCol = 'features', labelCol = 'label')

lrPipeline = Pipeline(stages = [indexer,encoder,assembler,lr])
lrModel = lrPipeline.fit(train)

lrPredictions = lrModel.transform(test)
lrSelected = lrPredictions.select('features','label','probability','prediction').show(10)
```



## Model Evaluation

### Compute metrics

```python
def compute_metrics(predictions):
    # to calculate accuracy,precision,recall and f1 based on above example
    TN = predictions.filter('prediction = 0 AND label = 0').count()
    ## predict positive when it positive
    TP = predictions.filter('prediction = 1 AND label = 1').count()
    #WRITE CODE to find the False Negative
    ## predict negtive when it positive
    FN = predictions.filter('prediction = 0 AND label = 1').count()
    #WRITE CODE to find the False Positive
    ## predict positive when it negtive 
    FP = predictions.filter('prediction = 1 AND label = 0').count()

    # calculate metrics by the confusion matrix
    #WRITE CODE : formula to find accuracy
    accuracy = (TP + TN)/ (TP + TN + FN + FP)
    #WRITE CODE : formula to find precision
    precision = TP/(TP + FP)
    #WRITE CODE : formula to find recall
    recall = TP/ (FN + TP)
    #WRITE CODE : formula to find F1-score
    f1 = 2/((1/recall)+(1/precision))
    
    return accuracy,precision,recall,f1  
```



### TPR and FPR

```python
from pyspark.ml.evaluation import BinaryClassificationEvaluator

# Evaluate model Decision Tree
evaluator = BinaryClassificationEvaluator(rawPredictionCol="rawPrediction")
auc_dt = evaluator.evaluate(dtPredictions)

# area under curve for Random Forest
auc_rf = evaluator.evaluate(rfPredictions)

# area under curve for Logistic Regression
auc_lr = evaluator.evaluate(lrPredictions)
```



## Text Pipeline API 

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer

# Prepare training documents from a list of (id, text, label) tuples.
training = spark.createDataFrame([
    (0, "win million dollar", 1.0),
    (1, "Office meeting", 0.0),
    (2, "Do not miss this opportunity", 1.0),
    (3, "update your password", 1.0),
    (4, "Assignment submission", 0.0)
], ["id", "text", "label"])

# Prepare test documents, which are unlabeled (id, text) tuples.
test = spark.createDataFrame([
    (5, "get bonus of 200 dollars"),
    (6, "change your bank password"),
    (7, "Next meeting is at 5pm"),
    (8, "Late submission"), 
    (9, "Daily newsletter")
], ["id", "text"])

# Configure an ML pipeline, which consists of three stages: tokenizer, hashingTF, and lr.
tokenizer = Tokenizer(inputCol="text", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.001)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

# Fit the pipeline to training documents.
model = pipeline.fit(training)

# Make predictions on test documents and print columns of interest.
prediction = model.transform(test)
selected = prediction.select("id", "text", "probability", "prediction")
for row in selected.collect():
    rid, text, prob, prediction = row
    print("(%d, %s) --> prob=%s, prediction=%f" % (rid, text, str(prob), prediction))

```

