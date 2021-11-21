# Exploratory data analysis

In this notebook, we study graphical and non graphical EDA.

### Part 1: Non-Graphical EDA:

#### Measures of centeral location:

```
Mean, Median, Mode
```

#### Measures for variability:

```
Range, SD, IQR
```

#### Correlation

We can extract most of the basic statistics using describe() function.

```python
import pandas as pd
import numpy as np
import seaborn as sns
```

```python
df1 = sns.load_dataset("titanic")
```

```python
df1.describe()
```

![截屏2021-01-28 下午9.23.15](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.23.15.png?token=AWS37JJOFT4AO4H45FUFVIDBTICQY)

### Part2: Graphical EDA:

```
* BarPlot, 
* Line plot
* BoxPlot
* Histogram
* Scatterplot 
```

### Basic Example:

```python
import random
random.seed(0)
fig = plt.figure(figsize=(60, 40)) 
N = 100
x = range(N)
y1 = [random.randint(0, 50) for i in x]
```

### 1. Bar Plot

```python
plt.bar([10, 20, 30], [5, 8, 2]) 
```

![截屏2021-01-28 下午9.27.09](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.27.09.png?token=AWS37JJJTASQAWSK2MOIHS3BTICQ6)

### 2. Histogram

```python
plt.hist(y1)
```

![截屏2021-01-28 下午9.27.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.27.55.png?token=AWS37JJ5PSDE2AUL6YS3XQ3BTICRC)

### 3. Boxplot

```python
y_outliers = y1+[-10]
plt.boxplot([y1,y_outliers])
plt.show()
```

![截屏2021-01-28 下午9.31.00](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.31.00.png?token=AWS37JJFPKLXBBFOLNRPFWLBTICRI)

### 4. Line plot

```python
plt.plot(x,y1,'-')
```

![截屏2021-01-28 下午9.31.30](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.31.30.png?token=AWS37JKDYDUKKGQKIC2X77LBTICRU)

### 5. Scatter plot

```python
plt.plot(x,y1,'.r')
```

![截屏2021-01-28 下午9.32.16](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.32.16.png?token=AWS37JNGYCNIHIEUIEJZGTLBTICRW)

## Example 1: Titanic Data

Load the dataframe

```python
import pandas as pd
import numpy as np
import seaborn as sns
df1=sns.load_dataset("titanic")

```

```python
# or plot
import matplotlib.pyplot as plt
%matplotlib inline

df1['sex'].value_counts().plot( kind='bar')
```

![截屏2021-01-28 下午9.33.12](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.33.12.png?token=AWS37JOZDYERMSYWIHDXXPDBTICSC)

```python
df1['embarked'].unique()
```

![截屏2021-01-28 下午9.51.12](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.51.12.png?token=AWS37JLK5EIFWFKDSSQ7PCDBTICSQ)



```python
df1['embarked'].value_counts() # notice that 'nan' doesn't count or isn't counted
```

![截屏2021-01-28 下午9.51.40](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.51.40.png?token=AWS37JM3SFJBAZYXNUCLLYTBTICSW)

```python
df1['fare'].value_counts()
```

![截屏2021-01-28 下午9.52.04](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.52.04.png?token=AWS37JN2RNLHTG4GEAWMM2TBTICSY)

```python
# look at fares, seems like a total mess
plt.plot(df1['fare'])
```

![截屏2021-01-28 下午9.52.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.52.36.png?token=AWS37JMSYHGP5QTHRJZYV4DBTICTC)

```python
# that's not quite right, it's showing all ~1300 fares left to right
# try another type of plot, histogram, change bins to get different views 

fig = plt.figure()
ax = fig.add_subplot(111)
ax.hist(df1['fare'], bins = 50, range = (df1['fare'].min(),df1['fare'].max()))
plt.title('Fare distribution')
plt.xlabel('Fare')
plt.ylabel('Count of Passengers')
plt.show()
```

![截屏2021-01-28 下午9.53.41](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.53.41.png?token=AWS37JNEEDNMXIXCGKGSTEDBTICTK)

```python
df1.boxplot('fare',by='pclass',sym='k.', figsize=(6, 10))
```

![截屏2021-01-28 下午9.54.11](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.54.11.png?token=AWS37JMIFNJ3H5HICG2DPQDBTICTU)

```python
# just look at fare
bp = df1.boxplot(column='fare',sym='k.')
```

![截屏2021-01-28 下午9.54.32](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.54.32.png?token=AWS37JLWD63OLFUAP3E7H4TBTICT4)

```python
# but there are three 'classes', separate them:
df1.boxplot(column='fare', by = 'pclass',sym='k.', figsize=(8, 8))
```

![截屏2021-01-28 下午9.55.45](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.55.45.png?token=AWS37JL5XESEO3Z5NDD3ND3BTICUA)

```python
# do similarly with ages
fig = plt.figure()
ax = fig.add_subplot(111)
ax.hist(df1['age'], bins = 10, range = (df1['age'].min(),df1['age'].max()))
plt.title('Age distribution')
plt.xlabel('Age')
plt.ylabel('Count of Passengers')
plt.show()
```

![截屏2021-01-28 下午9.56.07](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.56.07.png?token=AWS37JIGCVRVMW4BK3FELBLBTICUG)

```python
# just to complete the story... what were some of the factors for survival?


ct = pd.crosstab([df1.pclass, df1.sex], df1.survived.astype(bool))
ct.plot(kind='bar', stacked=True, color=['red','blue'], grid=False)
```

![截屏2021-01-28 下午9.56.34](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.56.34.png?token=AWS37JOUFDMWKC7R3LMZFADBTICUQ)

### Plotting non-numerical data

```python
df1['sex'].value_counts()
```

```python
fig, ax = plt.subplots()
df1['sex'].value_counts().plot(ax=ax, kind='bar')
```

![截屏2021-01-28 下午9.57.37](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.57.37.png?token=AWS37JKEIAF6DNTHLPONUHTBTICUQ)

```python
fig, ax = plt.subplots()
df1['embarked'].value_counts().plot(ax=ax, kind='bar')
```

![截屏2021-01-28 下午9.57.58](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.57.58.png?token=AWS37JKNCRZSJBNR67K4BSDBTICVM)

## Example 2

### Exploratory Plotting

```python
import seaborn as sns
%matplotlib inline
df1 = pd.read_pickle('df1.p')
```

```python
sns.distplot(df1['Age'])
```

![截屏2021-01-28 下午9.58.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.58.55.png?token=AWS37JLXGEPGFLOC3W5VPZTBTICVU)

```python
sns.distplot(df1['AQ'].map({'high':1,'low':0}))
```

![截屏2021-01-28 下午9.59.17](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.59.17.png?token=AWS37JMYADQ65EROI4LGLN3BTICVW)

```python
df1.plot(x='Years Educ',y='Age',kind='scatter')
```

![截屏2021-01-28 下午9.59.35](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.59.35.png?token=AWS37JMCZP2HR4DJEYY3NOLBTICWG)

```python
df1.plot(x='Years Educ',y='Age',kind='scatter',color=df1['AQ'].map({'high':1,'low':0}))
```

![截屏2021-01-28 下午9.59.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%889.59.55.png?token=AWS37JIFER6IUD272DOADPDBTICWE)

```python
df1.boxplot('Age',by='Gender')
```

![截屏2021-01-28 下午10.00.18](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%8810.00.18.png?token=AWS37JOFXUU26ZLETSCGUJTBTICWK)

```python
import matplotlib
matplotlib.style.use('ggplot')
df1.boxplot('Age',by='Gender')
```

![截屏2021-01-28 下午10.01.34](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-28%20%E4%B8%8B%E5%8D%8810.01.34.png?token=AWS37JKNEWOIRMUP4QGFUZ3BTICWO)

