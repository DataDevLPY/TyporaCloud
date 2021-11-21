

# Collaborative Filtering

Collaborative filtering (CF) is a technique commonly used to build personalized recommendations on the Web. Some popular websites that make use of the collaborative filtering technology include Amazon, Netflix, iTunes, IMDB, LastFM, Delicious and StumbleUpon. In collaborative filtering, algorithms are used to make automatic predictions about a user's interests by compiling preferences from several users.

```python
# Import needed libraries from pyspark
from pyspark import SparkConf

# Create Spark configuration object
master = 'local[*]'
app_name = 'Collaborative_filtering'

spark_conf = SparkConf().setMaster(master).setAppName(app_name)

# Create SparkSession
from pyspark import SparkContext
from pyspark.sql import SparkSession

spark = SparkSession.builder.config(conf = spark_conf).getOrCreate()
sc = spark.sparkContext
sc.setLogLevel('ERROR')
```



## Data Loading

```python
df_user_artist = spark.read.format('text')\
        .option('header',True)\
        .option('escape', ' ')\
        .load('user_artist_data.txt')
#df_user_artist.show(5)
split_col = split(df_user_artist.value, ' ')
df_user_artist = df_user_artist.withColumn('user_id', split_col.getItem(0))
df_user_artist = df_user_artist.withColumn('artist_id', split_col.getItem(1))
df_user_artist = df_user_artist.withColumn('playcount', split_col.getItem(2))
df_user_artist = df_user_artist.drop('value')

df_user_artist.toPandas().head()
```

![截屏2021-01-20 上午8.12.32](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114756.png?token=AWS37JN36EKMX3E62O2ZD7TBTJ7LK)

```python
from pyspark.sql.functions import when, col, isnan, count
df_user_artist.select([count(when(isnan(x) | col(x).isNull(), x)).alias(x) for x in df_user_artist.columns]).toPandas()
```

![截屏2021-01-21 下午7.48.19](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114963.png?token=AWS37JM72MC5OZMPXJNMKYTBTJ7LM)

```python
df_artist_data = spark.read.format('text')\
        .option('header', True)\
        .option('excape', ' ')\
        .load('artist_data.txt')
#df_artist_data.show()
split_col = split(df_artist_data.value, '\t')
df_artist_data = df_artist_data.withColumn('artist_id', split_col.getItem(0))
df_artist_data = df_artist_data.withColumn('artist_name', split_col.getItem(1))
df_artist_data = df_artist_data.drop('value')
df_artist_data.toPandas().head()
```

![截屏2021-01-21 下午7.53.27](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114623.png?token=AWS37JPAOYUHZ3NUDNOIISTBTJ7LS)

```python
df_artist_data.select([count(when(isnan(x) | col(x).isNull(), x)).alias(x) for x in df_artist_data.columns]).toPandas()
```

![截屏2021-01-21 下午8.22.03](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114163.png?token=AWS37JPVSK7G76GPLCUZVDLBTJ7LY)

```python
df_artist_alias = spark.read.format('text')\
        .option('header', True)\
        .option('excape', ' ')\
        .load('artist_alias.txt')
#df_artist_alias.show()
split_col = split(df_artist_alias.value, '\t')
df_artist_alias = df_artist_alias.withColumn('bad_id', split_col.getItem(0))
df_artist_alias = df_artist_alias.withColumn('good_id', split_col.getItem(1))
df_artist_alias = df_artist_alias.drop('value')
df_artist_alias.toPandas().head()
```

![截屏2021-01-21 下午8.22.28](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114272.png?token=AWS37JJSFMJYA6BTNWQRSHLBTJ7L6)

```python
df_artist_alias.select([count(when(isnan(x) | col(x).isNull(), x)).alias(x) for x in df_artist_alias.columns]).toPandas()
```

![截屏2021-01-21 下午8.22.48](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114505.png?token=AWS37JKPUYXB6S3XHEM47CTBTJ7MG)



## Data Preparation

The `df_user_artist` contains **bad ids**, so the **bad ids** in the `user_artist_data.txt` file need to be remapped to **goodids**. The mapping of **bad_ids** to **good_ids** is in **artist_alias.txt** file. The first task is to create a dictionary of the artist_alias, so that it can be passed over a **broadcast variable**.

Broadcast makes Spark send and hold in memory just one copy for each executor in the cluster. When there are thousands of tasks, and many execute in parallel on each executor, this can save significant network traffic and memory. But you cannot directly broadcast a dataframe, it has to be converted to a list first

```python
#After loading the artist_alias data to the dataframe, it is converted to a dictionary to be set as a broadcast variable
artist_alias = dict(df_artist_alias.collect())
```

```python
# For the dictionary artist_alias which contains key value pair of badid and goodid, create a broadcast variable called bArtistAlias.
bArtistAlias = sc.broadcast(artist_alias)
```

After the broadcast variable is created, a function to replace the badids by looking up the values from the broadcasted dictionary is implemented for the userArtistRDD.

```python
from pyspark.sql.functions import StringType
from pyspark.sql.functions import udf, struct

def lookup_correct_id(artist_id):
    finalArtistID = bArtistAlias.value.get(artist_id)
    if finalArtistID is None:
        finalArtistID = artist_id
    return finalArtistID

lookup_udf = udf(lookup_correct_id, StringType())

df_user_artist = df_user_artist.withColumn('artist_id',lookup_udf('artist_id'))
#df_user_artist.filter(col('artist_id') == 1252408).show()
df_user_artist.cache()
```



## Data Exploration

Write a query in the function below, to return top **N** artist for a user with user_id : `2062243`. You will need to join `user` and `user_artist` datasets on the common key `artist_id`. The sample output is given below.

```python
from pyspark.sql.types import IntegerType

def top_n_artists(artist, user_artist, user_id, limit):
    
    df_join = artist.join(user_artist, artist.artist_id == user_artist.artist_id)\
                    .filter(col('user_id') == user_id)\
                    .sort(user_artist.playcount.cast(IntegerType()), ascending = False)\
                    .limit(limit)
    return df_join   

top_n_artists(df_artist_data,df_user_artist,2062243,5).toPandas()
```

![截屏2021-01-22 上午11.33.45](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114928.png?token=AWS37JIM2CWTYBI3OQT2PZDBTJ7MU)

```python
#Cast the data column into integer types
def covert_type(df):
    
    for colname in df.columns:
        
        df_temp = df.withColumn(colname, df[colname].cast(IntegerType()))
        nan_count = df_temp\
            .select([count(when(isnan(colname) | col(colname).isNull(), colname))])\
            .take(1)[0][0]
        if nan_count == 0:
            df = df.withColumn(colname, df[colname].cast(IntegerType()))
    return df

df_user_artist = covert_type(df_user_artist)
df_artist_data = covert_type(df_artist_data)
df_artist_alias = covert_type(df_artist_alias)
```

## Train Test Split 

Create training and testing dataset with a 80/20 split

```python
final_df = df_artist_data\
    .join(df_user_artist, df_user_artist.artist_id == df_artist_data.artist_id)
final_df = final_df.drop(df_artist_data['artist_id'])
#final_df.show(2)
train, test = final_df.randomSplit([0.8,0.2])
```



## Model Building [[REF\]](https://spark.apache.org/docs/latest/ml-collaborative-filtering.html) 

Collaborative filtering is commonly used for recommender systems. These techniques aim to fill in the missing entries of a user-item association matrix. spark.ml currently supports model-based collaborative filtering, in which users and products are described by a small set of latent factors that can be used to predict missing entries. spark.ml uses the alternating least squares (ALS) algorithm to learn these latent factors. The implementation in spark.ml has the following parameters:

- **numBlocks** is the number of blocks the users and items will be partitioned into in order to parallelize computation (defaults to 10).
- **rank** is the number of latent factors in the model (defaults to 10).
- **maxIter** is the maximum number of iterations to run (defaults to 10).
- **regParam** specifies the regularization parameter in ALS (defaults to 1.0).
- **implicitPrefs** specifies whether to use the explicit feedback ALS variant or one adapted for implicit feedback data (defaults to false which means using explicit feedback).
- **alpha** is a parameter applicable to the implicit feedback variant of ALS that governs the baseline confidence in preference observations (defaults to 1.0).
- **nonnegative** specifies whether or not to use nonnegative constraints for least squares (defaults to false).



```python
from pyspark.ml.recommendation import ALS

als = ALS(maxIter=5,\
          implicitPrefs=True,\
          alpha=40,\
          userCol="user_id",\
          itemCol="artist_id",\
          ratingCol="playcount",\
          coldStartStrategy="drop")
```



Perform the following tasks.

- Train the model with the training set created from above.
- Then transform use the test data to get the predictions.
- Display the first 20 predictions from the results.

*The predictions shown below will be just indicator of how closely a given artist will be to the user's existing preferences*

```python
model = als.fit(train)
prediction = model.transform(test)
prediction.show(20, truncate = False)
```

![截屏2021-01-22 下午2.45.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114005.png?token=AWS37JKVJ2LGYVBLNARWOE3BTJ7M6)

## Evalutation of ALS 

We can evaluate ALS using RMSE (Root Mean Squared Error) using the RegressionEvaluator as shown below:

```python
from pyspark.ml.evaluation import RegressionEvaluator

evaluator = RegressionEvaluator(metricName = 'rmse',\
                                labelCol = 'playcount',\
                                predictionCol = 'prediction'
                               )
rmse = evaluator.evaluate(prediction)
print("Root-mean-square error = " + str(rmse))
```

![截屏2021-01-22 下午2.54.25](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220115204.png?token=AWS37JKKN5OVSU3VOV4SBH3BTJ7NI)

**NOTE:** If you run the above code, the RMSE you will observe is very high.

For implicit data, RMSE is not a reliable score since, we don't have any reliable feedback over if items are disliked. RMSE requires knowing which items the user dislikes. Spark does not have a readily available solution for to evaluate the implicit data. The following function implements ROEM (Rank Ordering Error Metric) on the prediction data. You can refer to the details about this [here](https://campus.datacamp.com/courses/recommendation-engines-in-pyspark/what-if-you-dont-have-customer-ratings?ex=6).

```python
def ROEM(predictions, userCol = "userId", itemCol = "songId", ratingCol = "num_plays"):
    #Creates table that can be queried
    predictions.createOrReplaceTempView("predictions")

    #Sum of total number of plays of all songs
    denominator = predictions.groupBy().sum(ratingCol).collect()[0][0]

    #Calculating rankings of songs predictions by user
    spark.sql("SELECT " + userCol + " , " + ratingCol + " , PERCENT_RANK() OVER (PARTITION BY " + userCol + " ORDER BY prediction DESC) AS rank FROM predictions").createOrReplaceTempView("rankings")

    #Multiplies the rank of each song by the number of plays and adds the products together
    numerator = spark.sql('SELECT SUM(' + ratingCol + ' * rank) FROM rankings').collect()[0][0]

    performance = numerator/denominator

    return performance
```

```python
ROEM(predictions,'user_id','artist_id','playcount')
```

![截屏2021-01-22 下午3.01.00](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220115986.png?token=AWS37JK5GEYQGHBE5BABFWDBTJ7NQ)

## Hyperparameter tuning and cross validation 

Since we can't use RMSE as the evaluation metric for "implicit data", we need to manually implement the hyperparameter tuning for the ALS. This code is adapted from the following source [[ref](https://github.com/jamenlong/ALS_expected_percent_rank_cv/blob/master/ROEM_cv.py)]

`alpha` is an important hyper-parameter for ALS with implicit feedback. It governs the baseline confidence in preference observations. It is a way to assign a confidence values to the `playcount`. Higher `playcount` would mean that we have higher confidence that the user likes that artist and lower `playcount` would mean the user doesn't like that much.

```python
def ROEM_cv(df, userCol = "user_id", itemCol = "artist_id", ratingCol = "playcount", ranks = [10], maxIters = [10], regParams = [.05], alphas = [10, 40]):
    
    from pyspark.sql.functions import rand
    from pyspark.ml.recommendation import ALS

    ratings_df = df.orderBy(rand()) #Shuffling to ensure randomness

    #Building train and validation test sets
    train, validate = df.randomSplit([0.8, 0.2], seed = 0)

    #Building 3 folds within the training set.
    test1, test2,test3 = train.randomSplit([0.33,0.33,0.33], seed = 1)
    train1 = test2.union(test3)
    train2 = test1.union(test2)
    train3 = test1.union(test3)
    

    #Creating variables that will be replaced by the best model's hyperparameters for subsequent printing
    best_validation_performance = 9999999999999
    best_rank = 0
    best_maxIter = 0
    best_regParam = 0
    best_alpha = 0
    best_model = 0
    best_predictions = 0

      #Looping through each combindation of hyperparameters to ensure all combinations are tested.
    for r in ranks:
        for mi in maxIters:
            for rp in regParams:
                for a in alphas:
                #Create ALS model
                    als = ALS(rank = r, maxIter = mi, regParam = rp, alpha = a, userCol=userCol, itemCol=itemCol, ratingCol=ratingCol,
                            coldStartStrategy="drop", nonnegative = True, implicitPrefs = True)

                    #Fit model to each fold in the training set
                    model1 = als.fit(train1)
                    model2 = als.fit(train2)
                    model3 = als.fit(train3)
                    
                    #Generating model's predictions for each fold in the test set
                    predictions1 = model1.transform(test1)
                    predictions2 = model2.transform(test2)
                    predictions3 = model3.transform(test3)
                    
                    #Expected percentile rank error metric function
                    def ROEM(predictions, userCol = userCol, itemCol = itemCol, ratingCol = ratingCol):
                        #Creates table that can be queried
                        predictions.createOrReplaceTempView("predictions")

                        #Sum of total number of plays of all songs
                        denominator = predictions.groupBy().sum(ratingCol).collect()[0][0]

                        #Calculating rankings of songs predictions by user
                        spark.sql("SELECT " + userCol + " , " + ratingCol + " , PERCENT_RANK() OVER (PARTITION BY " + userCol + " ORDER BY prediction DESC) AS rank FROM predictions").createOrReplaceTempView("rankings")

                        #Multiplies the rank of each song by the number of plays and adds the products together
                        numerator = spark.sql('SELECT SUM(' + ratingCol + ' * rank) FROM rankings').collect()[0][0]

                        performance = numerator/denominator

                        return performance

                    #Calculating expected percentile rank error metric for the model on each fold's prediction set
                    performance1 = ROEM(predictions1)
                    performance2 = ROEM(predictions2)
                    performance3 = ROEM(predictions3)
                    

                    #Printing the model's performance on each fold        
                    print("Model Parameters: \nRank:", r,"\nMaxIter:", mi, "\nRegParam:",rp,"\nAlpha: ",a)
                    print("Test Percent Rank Errors: ", performance1, performance2, performance3)

                    #Validating the model's performance on the validation set
                    validation_model = als.fit(train)
                    validation_predictions = validation_model.transform(validate)
                    validation_performance = ROEM(validation_predictions)

                    #Printing model's final expected percentile ranking error metric
                    print("Validation Percent Rank Error: "), validation_performance
                    print(" ")

                    #Filling in final hyperparameters with those of the best-performing model
                    if validation_performance < best_validation_performance:
                        best_validation_performance = validation_performance
                        best_rank = r
                        best_maxIter = mi
                        best_regParam = rp
                        best_alpha = a
                        best_model = validation_model
                        best_predictions = validation_predictions

    #Printing best model's expected percentile rank and hyperparameters
    print ("**Best Model** ")
    print ("  Percent Rank Error: ", best_validation_performance)
    print ("  Rank: ", best_rank)
    print ("  MaxIter: ", best_maxIter)
    print ("  RegParam: ", best_regParam)
    print ("  Alpha: ", best_alpha)
    
    return best_model, best_predictions
  
  
def ROEM_cv(df, userCol = "user_id", itemCol = "artist_id", ratingCol = "playcount", ranks = [10], maxIters = [10], regParams = [.05], alphas = [10, 40]):
    
    from pyspark.sql.functions import rand
    from pyspark.ml.recommendation import ALS

    ratings_df = df.orderBy(rand()) #Shuffling to ensure randomness

    #Building train and validation test sets
    train, validate = df.randomSplit([0.8, 0.2], seed = 0)

    #Building 3 folds within the training set.
    test1, test2,test3 = train.randomSplit([0.33,0.33,0.33], seed = 1)
    train1 = test2.union(test3)
    train2 = test1.union(test2)
    train3 = test1.union(test3)
    

    #Creating variables that will be replaced by the best model's hyperparameters for subsequent printing
    best_validation_performance = 9999999999999
    best_rank = 0
    best_maxIter = 0
    best_regParam = 0
    best_alpha = 0
    best_model = 0
    best_predictions = 0

      #Looping through each combindation of hyperparameters to ensure all combinations are tested.
    for r in ranks:
        for mi in maxIters:
            for rp in regParams:
                for a in alphas:
                #Create ALS model
                    als = ALS(rank = r, maxIter = mi, regParam = rp, alpha = a, userCol=userCol, itemCol=itemCol, ratingCol=ratingCol,
                            coldStartStrategy="drop", nonnegative = True, implicitPrefs = True)

                    #Fit model to each fold in the training set
                    model1 = als.fit(train1)
                    model2 = als.fit(train2)
                    model3 = als.fit(train3)
                    
                    #Generating model's predictions for each fold in the test set
                    predictions1 = model1.transform(test1)
                    predictions2 = model2.transform(test2)
                    predictions3 = model3.transform(test3)
                    
                    #Expected percentile rank error metric function
                    def ROEM(predictions, userCol = userCol, itemCol = itemCol, ratingCol = ratingCol):
                        #Creates table that can be queried
                        predictions.createOrReplaceTempView("predictions")

                        #Sum of total number of plays of all songs
                        denominator = predictions.groupBy().sum(ratingCol).collect()[0][0]

                        #Calculating rankings of songs predictions by user
                        spark.sql("SELECT " + userCol + " , " + ratingCol + " , PERCENT_RANK() OVER (PARTITION BY " + userCol + " ORDER BY prediction DESC) AS rank FROM predictions").createOrReplaceTempView("rankings")

                        #Multiplies the rank of each song by the number of plays and adds the products together
                        numerator = spark.sql('SELECT SUM(' + ratingCol + ' * rank) FROM rankings').collect()[0][0]

                        performance = numerator/denominator

                        return performance

                    #Calculating expected percentile rank error metric for the model on each fold's prediction set
                    performance1 = ROEM(predictions1)
                    performance2 = ROEM(predictions2)
                    performance3 = ROEM(predictions3)
                    

                    #Printing the model's performance on each fold        
                    print("Model Parameters: \nRank:", r,"\nMaxIter:", mi, "\nRegParam:",rp,"\nAlpha: ",a)
                    print("Test Percent Rank Errors: ", performance1, performance2, performance3)

                    #Validating the model's performance on the validation set
                    validation_model = als.fit(train)
                    validation_predictions = validation_model.transform(validate)
                    validation_performance = ROEM(validation_predictions)

                    #Printing model's final expected percentile ranking error metric
                    print("Validation Percent Rank Error: "), validation_performance
                    print(" ")

                    #Filling in final hyperparameters with those of the best-performing model
                    if validation_performance < best_validation_performance:
                        best_validation_performance = validation_performance
                        best_rank = r
                        best_maxIter = mi
                        best_regParam = rp
                        best_alpha = a
                        best_model = validation_model
                        best_predictions = validation_predictions

    #Printing best model's expected percentile rank and hyperparameters
    print ("**Best Model** ")
    print ("  Percent Rank Error: ", best_validation_performance)
    print ("  Rank: ", best_rank)
    print ("  MaxIter: ", best_maxIter)
    print ("  RegParam: ", best_regParam)
    print ("  Alpha: ", best_alpha)
    
    return best_model, best_predictions  
```

![截屏2021-01-22 下午3.05.02](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-22 下午3.05.02.png)

## Making Predictions 

The k-fold validation implement might take long time to run. You can use the initial ALS model to make the predictions. Assuming you have successfully trained the model, we want to now use the model to **find top Artists recommended for each user**. We can use the ***recommendForAllUsers*** function available in the ALS model to get the list of top recommendations for each users. You can further explore the details of the API [here](https://spark.apache.org/docs/2.2.0/api/python/pyspark.ml.html#pyspark.ml.recommendation.ALS).

The `recommendForAllUsers` only gives the list of artist_ids for the users, you can write the code to map these artist_ids back to their names.

```python
def recommendedArtists(als_model,user_id,limit):
    #get the recommendations
    test = model.recommendForAllUsers(limit).filter(col('user_id')==user_id)\
            .select("recommendations").collect()
    
    #create a dataframe for the top artist list
    id_rating_list = []
    for i in range(len(test[0][0])):
        artist_id = test[0][0][i][0]
        rating = test[0][0][i][1]
        id_rating_list.append((artist_id,rating))

    new_df = spark.createDataFrame(id_rating_list, ['artist_id','rating'])
    
    #join the top_artist dataframe with the artist master dataframe to include the artist_name
    final = new_df.join(df_artist, new_df.artist_id == df_artist.artist_id).select(new_df.artist_id,'artist_name')
            #.orderBy('rating')\
            #.select(new_df.artist_id,'artist_name')
    
    return final
    
recommendedArtists(model,2062243,20).show(truncate=False)
```







