## Data Discretization

### Binning:

Binning aims to discretise continuous values into discrete bins. We explain in the following different ways for implementing binning with Python.

### A. Using the digitize() method:

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

![截屏2021-02-01 上午9.37.41](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%889.37.41.png?token=AWS37JJVIVO6LZG23B6RWCLBTIAVU)

```python
data.min(), data.max(), data.mean(), data.std()
```

![截屏2021-02-01 上午9.39.11](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%889.39.11.png?token=AWS37JIXYPSVMNXAGTPKCZ3BTIAV4)

```python
digitized # so the 100 values are now group into 10 bins
```

![截屏2021-02-01 上午9.48.29](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%889.48.29.png?token=AWS37JLHGYRQRDJOUZVTMBTBTIAV6)



```python
# not so easy to look at so put them side by side in a DataFrame
import pandas as pd
df = pd.DataFrame({"Data" : data, "DigBin" : digitized})
```



```python
df.DigBin.value_counts().sort_index() 
# and there are the 10 bins,change the seed above from '1234' to something else to see 
```

![截屏2021-02-01 上午9.51.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%889.51.01.png?token=AWS37JLWQFPWGE5COOIJISLBTIAWE)

```python
df.head()
```

![截屏2021-02-01 上午9.51.25](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%889.51.25.png?token=AWS37JPIL57IS6XRF3KSXNTBTIAWO)

```python
df.sort_values("Data")
# df.sort_values("DigBin").head()
```

![截屏2021-02-01 上午9.51.57](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%889.51.57.png?token=AWS37JMJOOVFV2DQKJLHULTBTIAW2)

```python
# so all the little numbers ended up in bin 1, all the big ones in bin 10:
df.sort_values("DigBin").tail()
```

![截屏2021-02-01 上午9.52.27](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%889.52.27.png?token=AWS37JILSM27PULIM6TZKH3BTIAXC)

```python
%matplotlib inline
df.hist() # now we can see before and after (left to right), should have the same shape
# note the x scale 0..12 vs 0..1.0
```

![截屏2021-02-01 上午9.54.33](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%889.54.33.png?token=AWS37JJBLH5R6VVMGZ7O56LBTIAXK)



### B. Histograms...

You can also use histogram to do binning for you:

```python
binH = (np.histogram(data, bins, weights = data)[0] / np.histogram(data, bins)[0])
len(binH), binH # where binH is he mean of the values in each bin
```

![截屏2021-02-01 上午9.55.28](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%889.55.28.png?token=AWS37JPXPTMMNZ3ILQVWLELBTIAXY)



### C. Using scipy:

```python
# import numpy as np
from scipy.stats import binned_statistic
# we use the same data
binS = binned_statistic(data, data, bins = 10, range = (0, 1))[0]
```



![截屏2021-02-01 上午10.00.35](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8810.00.35.png?token=AWS37JPXTU7TC6G66BSZUOLBTIAYC)



```python
bc, be, bn = binned_statistic(data, None, statistic = 'count', bins = 10)
```

![截屏2021-02-01 上午10.01.06](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-02-01 上午10.01.06.png)

![截屏2021-02-01 上午10.01.19](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8810.01.19.png?token=AWS37JKLGZAQ53OMZ446LC3BTIAYS)

![截屏2021-02-01 上午10.01.40](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8810.01.40.png?token=AWS37JJ4SDB73ESC43VBPCLBTIAYW)

```
df["SciBin"] = bn # put side by side with the previous df
```



### D. Using Pandas Cut



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

![截屏2021-02-01 上午10.07.26](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8810.07.26.png?token=AWS37JJZJGGDN5GO2SJU5ULBTIAZA)



![截屏2021-02-01 上午10.07.43](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8810.07.43.png?token=AWS37JOY5O6MPGJJPYYJ53LBTIAZI)

which side is closed can be changed

![截屏2021-02-01 上午10.10.44](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8810.10.44.png?token=AWS37JP7P2YRVQ5FJ4OWFX3BTIAZM)

If you pass cut an integer number of bins instead of explicit bin edges, it will compute equal-length bins based on the minimum and maximum values in the data. Consider the case of some uniformly distributed data chopped into tenths same data as above, 100 randoms

![截屏2021-02-01 上午10.12.00](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8810.12.00.png?token=AWS37JI3YRJEL3NCZSFXOMDBTIA2M)

A closely related function, qcut, bins the data based on sample quantiles. Depending on the distribution of the data, using cut will not usually result in each bin having the same number of data points. Since qcut uses sample quantiles instead, by definition you will obtain roughly equal-size bins:

![截屏2021-02-01 上午10.13.57](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-02-01%20%E4%B8%8A%E5%8D%8810.13.57.png?token=AWS37JJQA75B26VCB5YLAMLBTIA2W)





