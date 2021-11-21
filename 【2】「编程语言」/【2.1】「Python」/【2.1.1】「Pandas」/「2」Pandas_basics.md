## 1. Pandas基本操作

```python
import pandas as pd
ufo = pd.read_csv('http://bit.ly/uforeports')
ufo.head()

ufo['City'].head()
ufo.City.head()
```

![截屏2021-01-25 下午3.55.55](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午3.55.55.png)

```python
ufo[['City','State']].head()
```

![截屏2021-01-25 下午3.58.32](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午3.58.32.png)

```python
ufo.loc[:,'City':'State'].head()
```

![截屏2021-01-25 下午3.58.55](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午3.58.55.png)

```python
ufo.iloc[1:6,2:6]
```

![截屏2021-01-25 下午3.59.39](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午3.59.39.png)

```python
ufo.City=='Ithaca'
```

![截屏2021-01-25 下午4.00.14](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.00.14.png)

```python
ufo[ufo.City == 'Ithaca']
```

![截屏2021-01-25 下午4.00.54](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.00.54.png)

```python
ufo[['City','State']][ufo.City == 'Ithaca']
```

![截屏2021-01-25 下午4.02.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.02.01.png)

## 2. Import以及Index

```python
Alcohol = pd.read_csv('http://apps.who.int/gho/athena/data/xmart.csv?target=GHO/SA_0000001400&profile=crosstable&filter=COUNTRY:*;YEAR:2012&x-sideaxis=COUNTRY;DATASOURCE;ALCOHOLTYPE&x-topaxis=GHO;YEAR',skiprows=1)

Alcohol.head(10)
```

![截屏2021-01-25 下午4.04.27](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.04.27.png)

```python
Alcohol[Alcohol['Beverage Types']==" Beer"].sort_values([" 2012"])
```

![截屏2021-01-25 下午4.09.50](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.09.50.png)

## 3. The way of using Pandas

First, a couple of rows have missing city data that I'll drop to simplify what we're doing.

```python
ufo[10:20]
```

![截屏2021-01-25 下午4.11.47](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.11.47.png)

```python
ufo.dropna(axis=0, inplace=True, how = 'any')
ufo[10:20]
```

![截屏2021-01-25 下午4.13.44](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.13.44.png)

```python
ufo.reset_index(inplace = True)
ufo[10:20]
```

### ![截屏2021-01-25 下午4.22.18](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.22.18.png)3.1 Creating a new column

I want to create a column with the combined City and State place names, I'll create a column called 'place' with an empty string in every row. This isn't absolutely necessary when using proper Pandas methods but for the demonstration it will make it more straight forward.

```python
uf0['place'] = ''
ufo.head()
```

![截屏2021-01-25 下午4.27.59](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.27.59.png)

### 3.2 Timing it

The Notebook magic %%timeit will run the cell 1000 times and get the 3 quickest times.

```python
%%timeit

# Using proper Pandas whole series operations
ufo['place'] = ufo['City'] + ', ' + ufo['State']
```

![截屏2021-01-25 下午4.29.16](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.29.16.png)

```python
ufo.head()
```

![截屏2021-01-25 下午4.29.42](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.29.42.png)















