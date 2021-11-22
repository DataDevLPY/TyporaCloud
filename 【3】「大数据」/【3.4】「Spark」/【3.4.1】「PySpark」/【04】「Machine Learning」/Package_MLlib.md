

# MLlib包

MLlib是Spark中实现机器学习功能的模块，其主要针对RDD对象与DStream流对象。在Spark 2.0中，新引入的ML包是主要针对DataFrame对象的机器学习包。MLlib目前处于维护状态。



## 读入数据

首先读入数据，因为我们的数据以csv的形式保存，因此先以DataFrame的形式读入内存。			

![截屏2021-01-19 下午12.24.43](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108946.png?token=AWS37JMVBBKQLCLJJTC366DBTJ6T2)

从数据中选取一个检测点作为我们要使用的点,可以看到该点共有2969条数据。数据读入时已经按照时间排序完成了，因此数据本身是保证了时序顺序的。

​			![截屏2021-01-19 下午12.25.34](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108188.png?token=AWS37JIU3DJN7LWMOHQJYMTBTJ6T6)



## 描述性统计

首先选择出要使用的属性并将其转换为RDD。转换为RDD后，每个RDD保存原DataFrame每一行的数据。

![截屏2021-01-19 下午12.27.47](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108147.png?token=AWS37JPK2VUVBKM66WJB3DDBTJ6US)

为筛选的列使用mlib的统计函数进行统计。(读入的时候需要对na值进行填充，否则包含na的列其统计信息也为na）

![截屏2021-01-19 下午12.28.41](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108690.png?token=AWS37JPNXGC4BQF3SCVJBMTBTJ6VA)

## 相关性

交通流数据之间具有较强的相关性，流量、速度、占有率之间可以按照特定的函数进行转化，我们使用mllib的相关性函数来查看三种属性之间的相关性。可以看出流量与占有率是正相关的，与速度是负相关的。

![截屏2021-01-19 下午12.30.11](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108087.png?token=AWS37JNWYWOSCVTYK5VNXALBTJ6VK)



## 回归预测

在这里我们使用随机森林进行回归预测，使用随机森林的好处是我们可以直接使用原始数据而不需要预处理。

首先我们读入数据，提取指定点的流量数据。

![截屏2021-01-19 下午12.30.35](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108043.png?token=AWS37JKRCYAOH5ANLP7RMSTBTJ6VW)



然后我们根据时滞lag=4来构建二维的数据集合，即用过去四个点预测未来一个点。通过**slide()**函数我们可以获得一个大小为(n, 5)的矩阵。

![截屏2021-01-19 下午12.31.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108250.png?token=AWS37JPROOLHNCMM5TCE4YDBTJ6V2)

然后我们将创建好的数据转化为RDD类型。

![截屏2021-01-19 下午12.31.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108169.png?token=AWS37JKYTYIWEREO63F3FSLBTJ6WC)

有了RDD类型之后，我们将其构建为Mllib中的模型能够使用的数据格式**LabeledPoint**。其参数如下，第一个值为预测值或者标签，后面的值为特征集合。

```python
labeled_v = volume.map(lambda row: LabeledPoint(row[-1], row[:-1]))
```



按照3：1划分训练集与测试集。

```python
train, test = labeled_v.randomSplit([0.75, 0.25])
```

构造决策树模型并进行训练

```python
model = DecisionTree.trainRegressor(train, {})
```

使用测试集进行预测，我们可以使用label或者features属性来访问LabeledPoint对象的属性。在这里预测出来的值要进行float转换，不换会出现`TypeError: DoubleType can not accept object in type <type 'numpy.float64'>`错误。

```python
model = DecisionTree.trainRegressor(train, {})
y_pred = model.predict(test.map(lambda row: row.features))\
        .map(lambda row: float(row))
```

提取真实值，并将预测值与真实值配对，以方便送入评估器。

```python
y_ture = test.map(lambda row: row.label)
# an RDD of (prediction, observation) pairs.
res = y_pred.zip(y_ture)
print(res.take(10))
eva(res)
```

Mllib提供了评估类用于评估模型效果，回归评估函数如下：

```python
def eva(res):
    metrics = ev.RegressionMetrics(res)

    print("Explained Variance:{0:.2f}".format(metrics.explainedVariance))
    print("R2:{0:.2f}".format(metrics.r2))
    print("MAE:{0:.2f}".format(metrics.meanAbsoluteError))
    print("RMSE:{0:.2f}".format(metrics.rootMeanSquaredError))
```



预测结果：

将写好的文件提交到spark，运行结果如下：

![截屏2021-01-19 下午12.36.43](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220108728.png?token=AWS37JLLCSAPYHBLFFGOWOTBTJ6WS)



完整代码：

```python
import pandas as pd
from pyspark.sql import SparkSession
import pyspark.mllib.evaluation as ev
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.tree import DecisionTree


def slide(data, lag):
    lag += 1
    res = []
    n = len(data)

    for i in range(lag, n):
        res.append(data[i - lag: i])

    return res


def eva(res):
    metrics = ev.RegressionMetrics(res)

    print("Explained Variance:{0:.2f}".format(metrics.explainedVariance))
    print("R2:{0:.2f}".format(metrics.r2))
    print("MAE:{0:.2f}".format(metrics.meanAbsoluteError))
    print("RMSE:{0:.2f}".format(metrics.rootMeanSquaredError))


def main():
    sc = SparkSession.builder.master("local").appName("Demo").getOrCreate()

    lag = 4
    df = pd.read_csv("E:\Documents\Desktop\data.csv", encoding='utf-8')
    volume = df[df.detectorid == 100625]['volume'].tolist()
    volume = slide(volume, lag)
    volume = sc.createDataFrame(volume)
    volume = volume.rdd.map(lambda row: [e for e in row])

    labeled_v = volume.map(lambda row: LabeledPoint(row[-1], row[:-1]))
    train, test = labeled_v.randomSplit([0.75, 0.25])

    model = DecisionTree.trainRegressor(train, {})
    y_pred = model.predict(test.map(lambda row: row.features))\
        .map(lambda row: float(row))
    y_ture = test.map(lambda row: row.label)
    # an RDD of (prediction, observation) pairs.
    res = y_pred.zip(y_ture)
    print(res.take(10))
    eva(res)


if __name__ == '__main__':
    main()
```

