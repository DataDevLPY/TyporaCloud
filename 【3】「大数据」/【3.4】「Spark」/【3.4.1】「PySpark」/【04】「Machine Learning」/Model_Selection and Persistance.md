# Activity: Machine Learning with Spark (Model Selection and Persistance)

### Accessing the parameters in the Model

You can use `extractParamMap()` to see the list of parameters for the particular estimater. For more details on this, refer to the [Spark Documentation](https://spark.apache.org/docs/latest/api/python/pyspark.ml.html). If it is a PipelineModel, you need to do `model.stages[-1].extractParamMap()`

## Cross Validation and Hyperparameter Tuning

Last week we looked into Decision Trees, out of the different parameters, let's take `maxBins` and `maxDepth` as the two hyperparamters. We used `maxDepth=3` as the default value, but **is 3 the optimum hyperparameter value for the model?**. This is something we want to achieve using the HyperParameter Tuning. We could also manually tune the hyperparameters using educated guesses, training the model, evaluating its performance and repeating the steps but it will be tedious. [Read More](https://towardsdatascience.com/cross-validation-and-hyperparameter-tuning-how-to-optimise-your-machine-learning-model-13f005af9d7d)

One popular approach is to create a grid of hyper-parameters we want to optimze with the values we want to try out. In Spark, we can use `ParamGridBuilder` to define the hyperparameters for any estimator. Since, the model needs to be evaluated at every parameter combination, we need to also define an `Evaluator`.

Finally, when we use this with the `CrossValidator (K-Fold)`, for each fold (i.e. the train/test split), it tries out all the possible combination of the hyper-parameters and evaluates the performance of each instance. Finally, based on the evaluaton, it gives us the best model i.e. the best combination of hyperparameters to use.

Let's try to tune the parameters for our `DecisionTree` Model. Since we are using the `Pipeline`, we can directly plugin the PipelineModel to the CrossValidator as well.

Let's build a grid specifying all the parameters and their values we want to test our model with. We are assigning a series of values for the hyperparameters `maxDepth` and `maxBins`.

```python
from pyspark.ml.tuning import ParamGridBuilder, CrossValidator,CrossValidatorModel
from pyspark.ml.evaluation import BinaryClassificationEvaluator
# Create ParamGrid for Cross Validation
dtparamGrid = (ParamGridBuilder()
             .addGrid(dt.maxDepth, [2, 5, 10, 20, 30])
             .addGrid(dt.maxBins, [10, 20, 40, 80, 100])
             .build())
```

```python
# Define an evaluator to be used for evaluating the model 
dtevaluator = BinaryClassificationEvaluator(rawPredictionCol="rawPrediction")
```

Finally, let's declare the `CrossValidator` which takes the estimator, paramgrid and evaluator as input. Also, we need to specify the number of folds we want to test against.

```python
# Create 3-fold CrossValidator
dtcv = CrossValidator(estimator = pipeline,
                      estimatorParamMaps = dtparamGrid,
                      evaluator = dtevaluator,
                      numFolds = 3)
```

This is where we train our cross-validator, as the CV trains/evaluates the model for every fold of data across all possible parameter combinations, **this step is very expensive and might take some time to finsh.**

```python
# Run cross validations
dtcvModel = dtcv.fit(train)
```

### Finding the Best Model

Now that we have finished running the CrossValidator, we can obtain the model with the best combination of hyperparameters using `.bestModel`. Also `bestModel.stages[-1]._java_obj.paramMap()` gives you the hyperparameteres with the optimum values selected by the CV.

```python
#Getting the best model and the optimum parameters selected from the Cross Validation 
bestModel= dtcvModel.bestModel
print(bestModel.stages)
print('Best Param for DT: ', bestModel.stages[-1]._java_obj.paramMap())

```

![截屏2021-01-17 上午11.54.15](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108749.png?token=AWS37JPDHVYAGN6F5QKSEA3BTJ6TC)



## Model Persistance (Saving and Loading the Model)

For simple models (i.e. without pipelines), you can simply save the model by using `model.save('path')` and load it using `.load('model_path')`.

You can also save and load whole PipelineModel in Spark using save/load methods. In the following example, we will save the **bestModel** we obtained from the **Model Selection** and Load it again.

```python
#Saves the model to the filesystem
bestModel.save('bank_subscriber_prediction_model')
```

```python
#Loading the Pipeline Model From the filesystem
from pyspark.ml import PipelineModel
pipelineModel = PipelineModel.load('bank_subscriber_prediction_model')
```



## TrainValidation Split

This is another aproach in Spark for hyper-parameter tuning. You can refer to the Spark Documentation for more details [[Ref\]](https://spark.apache.org/docs/latest/ml-tuning.html#train-validation-split). Compared to CrossValidation, this approach is less expensive since it evaluates each combination of parameters just once as opposed to k-times in CV. The example below demonstrates the use of TrainValidationSplit. We have used the same parameter combination with the same pipeline model and evaluator.

Note that, one input parameter that is different than CV is `trainRatio`, which specifies the split percentage for train/validation data. When you run this vs the Cross-Validation version, you will notice signficant difference in the time taken which is due to the fact that this approach only evaluates the combination of parameters once.



```python
from pyspark.ml.tuning import TrainValidationSplit
dtparamGrid = (ParamGridBuilder()
             .addGrid(dt.maxDepth, [2, 5, 10, 20, 30])
             .addGrid(dt.maxBins, [10, 20, 40, 80, 100])
             .build())

dttv = TrainValidationSplit(estimator = pipeline,
                      estimatorParamMaps = dtparamGrid,
                      evaluator = dtevaluator,
                      trainRatio = 0.8)
model = dttv.fit(train)
```



```python
bestModel_tv = model.bestModel
print(bestModel_tv.stages[-1]._java_obj.paramMap())
```

![截屏2021-01-17 上午11.56.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108739.png?token=AWS37JLEA7LFOMEBOBEX2G3BTJ6TO)





