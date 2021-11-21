## 1. Data cleansing process:

```python
#Basic scientific python libs
import pandas as pd
#import numpy as np
# Visualisation
import matplotlib as mpl
import matplotlib.pyplot as plt
import matplotlib.pylab as pylab
#import seaborn as sns
# Configure visualisations
%matplotlib inline
mpl.style.use( 'ggplot' )
#Notebook displace setting
from IPython.core.display import HTML
css = open('style/style-table.css').read() + open('style/style-notebook.css').read()
HTML('<style>{}</style>'.format(css))
```

```python
titanic = pd.read_csv("titanic_new.csv")
```

```python
print (titanic.shape) 
titanic.head()
```

![截屏2021-01-30 上午9.24.18](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.24.18.png?token=AWS37JOS2T2FV5PJID36NITBTICXC)



```python
titanic.info()
```

![截屏2021-01-30 上午9.31.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.31.01.png?token=AWS37JOVIPXF4V3VKT5SALTBTICXC)

```python
titanic.describe()
```

![截屏2021-01-30 上午9.36.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.36.31.png?token=AWS37JILFIALSGVNTLNPI6TBTICXK)

```python
titanic.describe(include=['O'])
```

![截屏2021-01-30 上午9.37.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.37.50.png?token=AWS37JNGN3SA66EXGDCXLBTBTICX6)

```python
# Lets seperate the titles from the name 
coltitle = titanic['name'].apply(lambda s: pd.Series({'title': s.split(',')[1].split('.')[0].strip(), 'lastName':s.split(',')[0].strip(), 'firstName':s.split(',')[1].split('.')[1].strip()}))
coltitle
```

![截屏2021-01-30 上午9.43.43](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-30 上午9.43.43.png)

```python
# Add the columns to the titanic dataframe
titanic = pd.concat([titanic, coltitle], axis=1) 
#Drop the Name column, but here we choose to keep it at the moment.
#titanic.drop('name', axis=1, inplace=True)
titanic.head()
```

![截屏2021-01-30 上午9.44.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.44.36.png?token=AWS37JIOKS7BAXKVOHAWNWLBTICYO)



```python
titanic.title.value_counts()
```

![截屏2021-01-30 上午9.46.08](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.46.08.png?token=AWS37JJMBDCNBTKU3CJCBZDBTICYU)

```python
titanic.title.replace({'Mlle':'Miss', 'the Countess':'Miss', 'Lady':'Miss', 'Mme':'Miss', 'Don':'Mr'},inplace=True)
```

```python
titanic.drop(['name'], axis=1, inplace=True)
```

![截屏2021-01-30 上午9.47.12](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.47.12.png?token=AWS37JN4Z7TEMV2HLGIVAATBTICZC)

```python
titanic.embark_town.replace({"Cherborg": "Cherbourg", "Cherbourge": "Cherbourg", "Southamtpon":"Southampton", "queenstown":"Queenstown"}, inplace=True)
```



The cross-tabulation of embark_town and embarked below proves the correspondence between the values of embark_town and those of embarked.

```python
pd.crosstab(titanic["embark_town"], titanic["embarked"])
```

![截屏2021-01-30 上午9.50.15](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.50.15.png?token=AWS37JNCJPWFCDKB3HNUXJTBTICZI)

```python
titanic.hist(by="who", column="age")
```

![截屏2021-01-30 上午9.54.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.54.01.png?token=AWS37JNU2KIMSOB5UVKF2JDBTICZO)

#### Are there any duplicated records?

If we assume that firstName, lastName and age can uniquely identify a passenger, we can then use the three values to check whether or not the dataset contains duplicated records.

```python
titanic[titanic.duplicated(["firstName", "lastName", "age"], keep=False)]
```

![截屏2021-01-30 上午9.56.03](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.56.03.png?token=AWS37JJHXCAUGOVWR3DX4X3BTICZY)

```python
titanic[titanic.duplicated(["firstName", "lastName", "age"], keep="first")]
```

![截屏2021-01-30 上午9.56.33](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-30%20%E4%B8%8A%E5%8D%889.56.33.png?token=AWS37JOR3QTHL6DJD2B27WTBTIC2E)

```python
titanic.drop_duplicates(["firstName", "lastName", "age"], keep='first', inplace=True)
```

