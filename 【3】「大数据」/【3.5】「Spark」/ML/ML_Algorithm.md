## ML Classification Models

### Decision Tree
Decision tree algorithms are said to be widely used because they process categorical data and are readily available in classification tasks by multiple classes. The goal of using a Decision Tree is to create a training model that can use to predict the class or value of the target variable by learning simple decision rules inferred from prior data(training data). The picture below shows some components in a decistion tree.

![截屏2021-01-10 上午11.53.03](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-10 上午11.53.03.png)

Decision trees use multiple algorithms to decide to split a node into two or more sub-nodes. The creation of sub-nodes increases the homogeneity of resultant sub-nodes. In other words, we can say that the purity of the node increases with respect to the target variable. The decision tree splits the nodes on all available variables and then selects the split which results in most homogeneous sub-nodes.

On the downside, decision trees are prone to overfitting. They can easily become over-complex which prevents them from generalizing well to the structure in the dataset. In that case, the model is likely to end up overfitting which is a serious issue in machine learning. To overcome this decision tree hyperparameters could be tuned. 

```python
from pyspark.ml.classification import DecisionTreeClassifier

dt = DecisionTreeClassifier(featuresCol = 'features', labelCol = 'label', maxDepth = 3)
#dtModel = #WRITE CODE : Use the fit method to train the model with the training data you created in Step 4
dtPipeline = Pipeline(stages = [indexer, encoder, assembler, dt])
dtModel = dtPipeline.fit(train)

dtPredictions = dtModel.transform(test)
dtPredictions.select('label','prediction','probability').show(15, truncate = False)
```



<h3>Random Forest</h3>

```python
from pyspark.ml.classification import RandomForestClassifier
#WRITE CODE : Create a Random Forest Classiication model and train it
rf = RandomForestClassifier(featuresCol = 'features', labelCol = 'label', numTrees = 10)
rfPipeline = Pipeline(stages = [indexer, encoder, assembler, rf])
rfModel = rfPipeline.fit(train)

rfPrediction = rfModel.transform(test)
rfPrediction.select('label','prediction','probability').show(15, truncate = False)
```



### Logistic Regression 

*Logistic Regression* is a classification algorithm. It is used to predict a binary outcome (1 / 0, Yes / No, True / False) given a set of independent variables. To represent binary/categorical outcome, we use dummy variables. You can also think of logistic regression as a special case of linear regression when the outcome variable is categorical, where we are using log of odds as dependent variable. In simple words, it predicts the probability of occurrence of an event by fitting data to a logit function.



```python
from pyspark.ml.classification import LogisticRegression

#WRITE CODE Create an initial model using the train set.
lr = LogisticRegression(featuresCol = 'features', labelCol = 'label')
lrPipeline = Pipeline(stages = [indexer, encoder, assembler, lr])
lrModel = lrPipeline.fit(train)

lrPrediction = lrModel.transform(test)
lrPrediction.select('label','prediction','probability').show(15, truncate = False)
```



<h3>Model Evaluation</h3>

```python
# Calculate the elements of the confusion matrix
## predict negtive when it negtive
TN = dtPredictions.filter('prediction = 0 AND label = 0').count()
## predict positive when it positive
TP = dtPredictions.filter('prediction = 1 AND label = 1').count()
#WRITE CODE to find the False Negative
## predict negtive when it positive
FN = dtPredictions.filter('prediction = 0 AND label = 1').count()
#WRITE CODE to find the False Positive
## predict positive when it negtive 
FP = dtPredictions.filter('prediction = 1 AND label = 0').count()

# show confusion matrix
dtPredictions.groupBy('label', 'prediction').count().show()
# calculate metrics by the confusion matrix
#WRITE CODE : formula to find accuracy
accuracy = (TP + TN)/ (TP + TN + FN + FP)
#WRITE CODE : formula to find precision
precision = TP/(TP + FP)
#WRITE CODE : formula to find recall
recall = TP/ (FN + TP)
#WRITE CODE : formula to find F1-score
f1 = 2/((1/recall)+(1/precision))

#WRITE CODE : Display the various metrics calculated above
## how to display?
print(f'accuracy: {accuracy}\nprecision:{precision}\nrecall:{recall}\nf1:{f1}')
```



```python
def compute_metrics(predictions):
    #WRITE CODE: to calculate accuracy,precision,recall and f1 based on above example
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
  
  
confusion_matrix = compute_metrics(dtPredictions)
print(confusion_matrix)
```



Present the accuracies, precision and recall of the different classification algorithms in a bar chart using `matplotlib`. You can use the function given here:

```python
import matplotlib.pyplot as plt
%matplotlib inline
def plot_metrics(x,y):
    plt.style.use('ggplot')   
    x_pos = [i for i, _ in enumerate(x)]
    plt.bar(x_pos, y, color='blue')
    plt.xlabel("Classification Algorithms")
    plt.ylabel("AUC")
    plt.title("Accuracy of ML Classification Algorithms")
    plt.xticks(x_pos, x)
    plt.show()
    
x = (0.6438658428949691, 0.6117440841367222, 0.6572504708097928, 0.6336813436223332)
y = ('accuracy','precision','recall','f1-scores')
plot_metrics(y,x)
```

![截屏2021-01-10 下午4.35.35](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-10 下午4.35.35.png)



#### Area Under the Curve (AUC-ROC) 

![截屏2021-01-10 下午4.36.26](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-10 下午4.36.26.png)

##### Binary Classification Metrics 

Now let's evaluate the model using BinaryClassificationMetrics class in Spark ML. BinaryClassificationMetrics by default uses areaUnderROC as the performance metric.

```python
# Use BinaryClassificationEvaluator to evaluate a model
from pyspark.ml.evaluation import BinaryClassificationEvaluator

# Evaluate model Decision Tree
evaluator = BinaryClassificationEvaluator(rawPredictionCol="rawPrediction")
auc_dt = evaluator.evaluate(dtPredictions)
print(auc_dt)
print(evaluator.getMetricName())


#WRITE CODE : area under curve for Random Forest
auc_rf = evaluator.evaluate(rfPrediction)
print(auc_rf)


#WRITE CODE : area under curve for Logistic Regression
auc_lr = evaluator.evaluate(lrPrediction)
print(auc_lr)
```



### Visualizing AUC-ROC 

We can easily visualize the ROC curve for **Logistic Regression** using the `BinaryClassificationEvaluator`. The example below shows how to plot it using matplotlib.

```python
import matplotlib.pyplot as plt

print("Area Under ROC: " + str(evaluator.evaluate(lrPrediction, {evaluator.metricName: "areaUnderROC"})))

# Plot ROC curve
trainingSummary = lrModel.stages[-1].summary
roc = trainingSummary.roc.toPandas()
plt.plot(roc['FPR'],roc['TPR'])
plt.ylabel('TPR')
plt.xlabel('FPR')
plt.title('ROC Curve')
plt.show()
```

![截屏2021-01-10 下午4.38.16](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-10 下午4.38.16.png)











