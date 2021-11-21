# Clustering using K-Means

```python
# Import libraries from needed pyspark
from pyspark import SparkConf

# Create spark configuration object
master = "local[*]"
app_name = "K-Mean"
spark_conf = SparkConf().setMaster(master).setAppName(app_name)

# Create SparkSession
from pyspark import SparkContext
from pyspark.sql import SparkSession

spark = SparkSession.builder.config(conf = spark_conf).getOrCreate()
sc = spark.sparkContext
sc.setLogLevel("ERROR")
```

## Example

```python
from pyspark.ml.feature import StringIndexer
from pyspark.ml.feature import OneHotEncoder
from pyspark.ml.feature import VectorAssembler
from pyspark.ml import Pipeline
from pyspark.ml.clustering import KMeans
from pyspark.ml.evaluation import ClusteringEvaluator


#Step 1 : Prepare the Data
df = spark.createDataFrame(
 [("a@email.com", 12000,"M"),
    ("b@email.com", 43000,"M"),
    ("c@email.com", 5000,"F"),
    ("d@email.com", 60000,"M"),
    ("e@email.com", 55000,"M"),
    ("f@email.com", 11000,"F")
 ],
 ["email",'income','gender'])

#Step 2 : Feature Engineering 
indexer = StringIndexer(inputCols=['email','gender'],outputCols=['email_index','output_index'])
encoder = OneHotEncoder(inputCols=['email_index','output_index'],outputCols=['email_vec','output_vec'])
assembler = VectorAssembler(inputCols=['email_vec','output_vec','income'],outputCol='features')
#Create a KMeans Model Estimator initialized with 2 clusters
k_means = KMeans(featuresCol='features', k=2)

#Step 3 : Pipeline API and ML Model
pipeline = Pipeline(stages = [indexer,encoder,assembler,k_means])
pipelineModel = pipeline.fit(df)

# Make predictions
predictions = pipelineModel.transform(df)
predictions.show()
# # Evaluate clustering by computing Silhouette score
evaluator = ClusteringEvaluator()

silhouette = evaluator.evaluate(predictions)
print("Silhouette with squared euclidean distance = " + str(silhouette))

# Shows the result.
#HINT:You can use model.stages[-1] to access the model from the pipeline.
centers = pipelineModel.stages[-1].clusterCenters()
print("Cluster Centers: ")
for center in centers:
    print(center)
```

![截屏2021-01-16 下午12.40.45](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午12.40.45.png)



## Problem Statement

```python
# Step 1: Loading dataset
hack_data = spark.read.format("csv")\
				.option("header", True)\
  			.option("escape", True)\
    		.option("inferSchema", True)\
      	.load("hack_data.csv")
```



```python
# Step 2: Data Preparation
cols = [col for col in hack_data.columns if col != 'Location']
```



```python
# Step 3: Feature Engineering

## Assemble using VectorAssembler
assembler = VectorAssembler(inputCols=cols, outputCol='features')
assembled_data = assembler.transform(data)

## Feature scaling : Standard Scaler
# Next, we need to standardise our data. To accomplish this, Spark has its own StandardScaler which takes in two arguments — the name of the input column and the name of the output (scaled) column.
from pyspark.ml.feature import StandardScaler

scaler = StandardScaler(inputCol = "features", outputCol = "scaledFeatures")
scaler_model = scaler.fit(assembled_data)
scaler_data = scaler_model.transform(assembled_data)

```



```python
# Step 4: Clustering using K-Means 
k_mean = KMeans(featuresCol = 'scaledFeatures', k = 5)
model = k_mean.fit(scaler_data)
predictions = model.transform(scaler_data)
#predictions.toPandas().head()
predictions.groupby('prediction').count().show()
```

![截屏2021-01-16 下午8.29.55](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午8.29.55.png)



```python
# Step 5 : Pipeline

#Define the pipeline
pipeline = Pipeline(stages = [assembler, scaler, k_means])
KModel = pipeline.fit(hack_data)
predictions = KModel.transform(hack_data)
predictions.groupby('prediction').count().show()
```



```python
# Step 6: Silhouette Score

from pyspark.ml.evaluation import ClusterEvaluator
evaluator = ClusteringEvaluator()
silhouette = evaluator.evaluate(predictions)


```

* si接近1，则说明样本i聚类合理；
* si接近-1，则说明样本i更应该分类到另外的簇；

​              若si 近似为0，则说明样本i在两个簇的边界上。



### Finding optimal number of clusters based on the Silhouette Score 

We already know, better Silhouette Score signifes a better separation of clusters. To find the optimal number of clusters, we can simply calculate the score for different cluster size and decide the optimal number of clusters based on the maximum score.

In the code below, we have calculated the Silhouette score for 2-10 cluster size.

```python
#Here we are taking the data transformed by the StandardScaler
silhouette_arr=[]
for k in range(2,10):
    k_means= KMeans(featuresCol='scaledFeatures', k=k)
    model = k_means.fit(scaled_data)
    predictions = model.transform(scaled_data)
    silhouette = evaluator.evaluate(predictions)
    silhouette_arr.append(silhouette)
    print('No of clusters:',k,'Silhouette Score:',silhouette)
```

![截屏2021-01-16 下午8.48.04](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午8.48.04.png)

```python
#Visualizing the silhouette scores in a plot
import matplotlib.pyplot as plt
fig, ax = plt.subplots(1,1, figsize =(8,6))
ax.plot(range(2,10),silhouette_arr)
ax.set_xlabel('k')
ax.set_ylabel('cost')
```

![截屏2021-01-16 下午8.57.12](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午8.57.12.png)









