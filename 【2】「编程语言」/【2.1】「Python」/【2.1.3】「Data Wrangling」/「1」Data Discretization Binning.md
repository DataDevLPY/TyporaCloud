# Data Discretization

# Binning:

Binning aims to discretise continuous values into discrete bins. We explain in the following different ways for implementing binning with Python.

## A. Using the digitize() method:

```python
import numpy as np
np.random.seed(1234) # make it reproducible

n = 100 # how much data
data = np.random.random(n) # n random numbers on 0..1

bins = np.linspace(0, 1, 11) # equally spaced bins, from 0 to 1.0 
# 11 bin 'edges' or boundaries, gives us 10 bins

digitized = np.digitize(data, bins) # put the n in the b

```

```python
len(bins), bins # there are 11 bin boundaries or 'edges', i.e. 10 bins 
# 0 to 0.1... 0.9, 1.0 i.e. 10 bins, it's tidier
```

![截屏2021-02-01 上午9.37.41](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午9.37.41.png)

```python
data.min(), data.max(), data.mean(), data.std()
```

![截屏2021-02-01 上午9.39.11](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午9.39.11.png)

```python
digitized # so the 100 values are now group into 10 bins
```

![截屏2021-02-01 上午9.48.29](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午9.48.29.png)



```python
# not so easy to look at so put them side by side in a DataFrame
import pandas as pd
df = pd.DataFrame({"Data" : data, "DigBin" : digitized})
```



```python
df.DigBin.value_counts().sort_index() 
# and there are the 10 bins,change the seed above from '1234' to something else to see 
```

![截屏2021-02-01 上午9.51.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午9.51.01.png)

```python
df.head()
```

![截屏2021-02-01 上午9.51.25](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午9.51.25.png)

```python
df.sort_values("Data")
# df.sort_values("DigBin").head()
```

![截屏2021-02-01 上午9.51.57](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午9.51.57.png)

```python
# so all the little numbers ended up in bin 1, all the big ones in bin 10:
df.sort_values("DigBin").tail()
```

![截屏2021-02-01 上午9.52.27](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午9.52.27.png)

```python
%matplotlib inline
df.hist() # now we can see before and after (left to right), should have the same shape
# note the x scale 0..12 vs 0..1.0
```

![截屏2021-02-01 上午9.54.33](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午9.54.33.png)



## B. Histograms...

You can also use histogram to do binning for you:

```python
binH = (np.histogram(data, bins, weights = data)[0] / np.histogram(data, bins)[0])
len(binH), binH # where binH is he mean of the values in each bin
```

![截屏2021-02-01 上午9.55.28](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午9.55.28.png)

## C. Using scipy:

```python
# import numpy as np
from scipy.stats import binned_statistic
# we use the same data
binS = binned_statistic(data, data, bins = 10, range = (0, 1))[0]
```



![截屏2021-02-01 上午10.00.35](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.00.35.png)



```python
bc, be, bn = binned_statistic(data, None, statistic = 'count', bins = 10)
```

![截屏2021-02-01 上午10.01.06](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.01.06.png)

![截屏2021-02-01 上午10.01.19](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.01.19.png)

![截屏2021-02-01 上午10.01.40](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.01.40.png)

```
df["SciBin"] = bn # put side by side with the previous df
```



## D. Using Pandas Cut

## Example: ages dataset

```python
ages = [20, 22, 25, 26, 21, 23, 37, 31, 61, 45, 41, 32]
```

```python
# Let’s divide these into bins of 18 to 25, 26 to 35, 35 to 60, and finally 60 and older. 
# To do so, you can use 'cut', a function in pandas:
bins = [18, 25, 35, 60, 100] # as above 5 numbers give us 4 bins
cats = pd.cut(ages, bins)
cats
```

![截屏2021-02-01 上午10.07.26](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.07.26.png)



![截屏2021-02-01 上午10.07.43](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.07.43.png)

which side is closed can be changed

![截屏2021-02-01 上午10.10.44](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.10.44.png)

If you pass cut an integer number of bins instead of explicit bin edges, it will compute equal-length bins based on the minimum and maximum values in the data. Consider the case of some uniformly distributed data chopped into tenths same data as above, 100 randoms

![截屏2021-02-01 上午10.12.00](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.12.00.png)

A closely related function, qcut, bins the data based on sample quantiles. Depending on the distribution of the data, using cut will not usually result in each bin having the same number of data points. Since qcut uses sample quantiles instead, by definition you will obtain roughly equal-size bins:

![截屏2021-02-01 上午10.13.57](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.13.57.png)





