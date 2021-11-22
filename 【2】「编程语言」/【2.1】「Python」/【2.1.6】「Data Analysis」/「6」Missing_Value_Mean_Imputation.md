

```python
#import library 
import pandas as pd
import numpy as np
#load the data 
df1 = pd.read_csv('titanic passenger list.csv') 
```

```python
df1.dtypes
df1.info()
```

## Task 1 Check missing values

```python
df1.describe(include=['O']) # 'O' for Objects
```

![截屏2021-01-30 上午10.20.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028577.png)

```python
df1.isnull().sum()
```

![截屏2021-01-30 上午10.21.06](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028468.png)

## Task 2 investigate with missing values for ages

```python
np.mean(df1.age)
```

```python
# age - we know there are some missing, let's dig deeper
df1['age'].value_counts()
```

```python
df1[df1['age'].isnull()].head() # look at some
```

![截屏2021-01-30 上午10.27.13](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028290.png)

```python
df1.boxplot(by = 'pclass')
```

![截屏2021-01-30 上午10.27.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028921.png)

```python
df1.loc[:,['age','pclass']].boxplot(by = 'pclass')
```

![截屏2021-01-30 上午10.28.34](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028546.png)



### Let's look at other factors, e.g. names can give a clue because most passengers seem to have a title (e.g. 'Mrs')

### Discussion:

* Can we assume that 'Mrs' implies married (implies not a child)?

* What about 'Miss' or 'Ms'?

```python
df1['title'] = df1['name'].str.extract(", (\w*).").head()
```



```python
# Look into titles, e.g. 'Mrs' implies married (implies not child)
def name_extract(word):
     return word.split(',')[1].split('.')[0].strip()
    
# because names are in this format:
# Allison, Master. Hudson Trevor
# we can split on ','
# then '.'

temp = pd.DataFrame({'Title':df1['name'].apply(name_extract)}) # testing, apply the method to the data
# check unique values
temp['Title'].unique()
```

### That worked really well, we have titles for everyone and there are only a few strange ones:

Someone look up 'Jonkheer' (and Dona - Spanish?)

#### So what can we say about 'Master'? Usually refers to boys?

#### Dr, Rev, Col, Major, Capt, Sir, Don, would all be adults?

Let's go ahead and apply this transformation:



```python
df2 = df1 # copy then insert new column
df2['Title'] = df1['name'].apply(name_extract)
df2.head() # title at far right
```

```python
df2[(df2['Title'] == "Dr") & (df2['sex'] == "male")].mean()
```

![截屏2021-01-30 上午10.38.11](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028835.png)

## Task 3 Mean imputation of age by title

```python
# this seems too easy, is it right? 
df2["age"].fillna(df2.groupby("Title")["age"].transform("mean"), inplace=True)
df2.age.describe()
```

![截屏2021-01-30 上午10.40.09](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230029548.png)

