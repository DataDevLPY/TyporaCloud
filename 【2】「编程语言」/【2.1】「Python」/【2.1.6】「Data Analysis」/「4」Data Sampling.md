# Data Sampling

Here we discuss data sampling. Simple Random Sampling methods are easy to implement. We focus in this exercise on Stratified sampling.

We load wine.csv file, please note this wine data is different from the one used later in this module.

```python
import pandas as pd
import numpy as np
# UCI's wine dataset
wine = pd.read_csv("wine.csv")
wine.head()
```

![截屏2021-02-01 上午11.58.32](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.58.32.png)

![截屏2021-02-01 上午11.58.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.58.42.png?token=AWS37JIZQO25VEHIDIVHY3LBTIDD2)

![截屏2021-02-01 上午11.59.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.59.02.png?token=AWS37JOLP65DTVUUDB6XVM3BTIDD6)

![截屏2021-02-01 上午11.59.13](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8811.59.13.png?token=AWS37JMMVJMYGXLMNHXQ3IDBTIDEG)



## Stratified sampling:

Now we can take another random sample stratified by quality.

```python
# Stratified Split of train and test data

#For sklearn 0.17
#from sklearn.cross_validation import StratifiedShuffleSplit
#sample = StratifiedShuffleSplit(wine.quality, n_iter=1, test_size=0.5)
#sample = sss.split(wine.quality)



#For sklearn 0.20
from sklearn.model_selection import StratifiedShuffleSplit
sss =  StratifiedShuffleSplit(n_splits=1, test_size=0.5, random_state=0)
sample = sss.split(wine.quality, wine.quality)
## check 
sample
```



```python
#loop elements in sample
for train_index, test_index in sample:
    print ("train index :") 
    print(train_index)
    print ("test index:")
    print (test_index)
    xtrain = wine.iloc[train_index]
    xtest = wine.iloc[test_index]

```

![截屏2021-02-01 上午11.59.55](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午11.59.55.png)







