```python
import pandas as pd
import numpy as np

df1 = pd.read_csv('titanic passenger list.csv') 
```

```python
# We can boxplot an entire DataFrame, quick & nasty, ignores non-numeric data too
import matplotlib.pyplot as plt
%matplotlib inline

bp = df1.boxplot()
```



## Task 2 investigate outliers for "fare"

It seems that there are many extreme values for "fare". in this task

```python
bp = df1.boxplot(column='fare')
# Sometimes you have to force display of outliers:
# bp = plt.boxplot(df1.fare, 0, 'ro') # red circle
```

![截屏2021-01-30 下午9.47.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230029321.png)

```python
# so how do you get 512.3292 from £512, 6s
# 6shillings is ~1/3 pound?
# 6/20 is 0.3 so that may explain the 0.3292..
def poundit(p,s,d):
    return p + (d / 12.0 + s) / 20
# force the 12.0 not the 20!
# There were 20 shillings per pound and the shilling was divided into 12 pennies.

poundit(512,6,0) # £512, 6s
```

So 512.3 is a real value.

### Discussion:

- Is there any problem with this value?
- Should we treat it as an outlier?

### Let's investigate outliers by each class.

```python
# plot by class
bp = df1.boxplot(column='fare', by = 'pclass')
```

![截屏2021-01-30 下午9.48.20](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230029551.png)









