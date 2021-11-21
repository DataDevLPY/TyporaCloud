## 1. Pandas基本操作

```python
import pandas as pd
ufo = pd.read_csv('http://bit.ly/uforeports')
ufo.head()

ufo['City'].head()
ufo.City.head()
```

![截屏2021-01-25 下午3.55.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220010515.png?token=AWS37JI4ENT2F4FGG6J3ETLBTJXZY)

```python
ufo[['City','State']].head()
```

![截屏2021-01-25 下午3.58.32](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220010091.png?token=AWS37JPOO4KL6ZJ3HDOZ4U3BTJXZ4)

```python
ufo.loc[:,'City':'State'].head()
```

![截屏2021-01-25 下午3.58.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220010374.png?token=AWS37JP3OQU522REMNL7AM3BTJX2C)

```python
ufo.iloc[1:6,2:6]
```

![截屏2021-01-25 下午3.59.39](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220010767.png?token=AWS37JIYYPUCKOOJZXY2OGTBTJX2Q)

```python
ufo.City=='Ithaca'
```

![截屏2021-01-25 下午4.00.14](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220010813.png?token=AWS37JK4SDK35Z7YG5NDJNDBTJX22)

```python
ufo[ufo.City == 'Ithaca']
```

![截屏2021-01-25 下午4.00.54](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220010523.png?token=AWS37JKIPQUW4DXOFQ333OLBTJX3A)

```python
ufo[['City','State']][ufo.City == 'Ithaca']
```

![截屏2021-01-25 下午4.02.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220010049.png?token=AWS37JP56LIPYKGC6KGWOHTBTJX3I)

## 2. Import以及Index

```python
Alcohol = pd.read_csv('http://apps.who.int/gho/athena/data/xmart.csv?target=GHO/SA_0000001400&profile=crosstable&filter=COUNTRY:*;YEAR:2012&x-sideaxis=COUNTRY;DATASOURCE;ALCOHOLTYPE&x-topaxis=GHO;YEAR',skiprows=1)

Alcohol.head(10)
```

![截屏2021-01-25 下午4.04.27](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220010433.png?token=AWS37JLIHO4LT4KMG5ATDU3BTJX4C)

```python
Alcohol[Alcohol['Beverage Types']==" Beer"].sort_values([" 2012"])
```

![截屏2021-01-25 下午4.09.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220010600.png?token=AWS37JLXQMUDMCB7AVZ4TTDBTJX4Y)

## 3. The way of using Pandas

First, a couple of rows have missing city data that I'll drop to simplify what we're doing.

```python
ufo[10:20]
```

![截屏2021-01-25 下午4.11.47](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220011314.png?token=AWS37JJFDEAEPSYV7SZYGELBTJX5O)

```python
ufo.dropna(axis=0, inplace=True, how = 'any')
ufo[10:20]
```

![截屏2021-01-25 下午4.13.44](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220011246.png?token=AWS37JOPLO3LDITIRX3BOJDBTJX54)

```python
ufo.reset_index(inplace = True)
ufo[10:20]
```

### ![截屏2021-01-25 下午4.22.18](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220011940.png?token=AWS37JIZB6OAY4LGQ4M6343BTJX56)3.1 Creating a new column

I want to create a column with the combined City and State place names, I'll create a column called 'place' with an empty string in every row. This isn't absolutely necessary when using proper Pandas methods but for the demonstration it will make it more straight forward.

```python
uf0['place'] = ''
ufo.head()
```

![截屏2021-01-25 下午4.27.59](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220011339.png?token=AWS37JM7PGZSPGU3MGZP23DBTJX6I)

### 3.2 Timing it

The Notebook magic %%timeit will run the cell 1000 times and get the 3 quickest times.

```python
%%timeit

# Using proper Pandas whole series operations
ufo['place'] = ufo['City'] + ', ' + ufo['State']
```

![截屏2021-01-25 下午4.29.16](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220011874.png?token=AWS37JNWKKNUUG6JH6DEW2LBTJX64)

```python
ufo.head()
```

![截屏2021-01-25 下午4.29.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220011617.png?token=AWS37JPMNG2NFLHWBV2O3QDBTJX7Q)















