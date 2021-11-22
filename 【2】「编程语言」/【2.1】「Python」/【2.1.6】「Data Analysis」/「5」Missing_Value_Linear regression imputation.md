```python
import pandas as pd
from sklearn.linear_model import LinearRegression
from sklearn.datasets import load_boston
%matplotlib inline
```

```python
boston = load_boston() # Dataset from the sklearn library
```

```python
print (boston.DESCR)
```

```python
#from sklearn.cross_validation import train_test_split
from sklearn.model_selection import train_test_split
```



# This dataset has no missing values

we will select a feature to degrade (remove some values as missing data)

```python
X_train, X_test, y_train, y_test = train_test_split(boston_df.iloc[:,:-1],boston_df.iloc[:,-1:], random_state = 1)
```



These testing and training sets form our full dataset, we have prepared some data with missing vaues

# AGE

proportion of owner-occupied units built prior to 1940

```python
boston_dropna_df = pd.read_pickle('boston_dropna_df.p') #read the data into two dataframes
```

```python
boston_dropna_df.head()
```

![截屏2021-01-30 下午9.28.27](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028420.png)

```python
boston_impute_df = boston_dropna_df.copy()
```

```python
boston_dropna_df['AGE'].isnull().sum()
```

![截屏2021-01-30 下午9.29.51](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-30 下午9.29.51.png)

### boston_dropna_df will be a dataset to see what would happen if we just dropped rows with missing values -

```python
boston_dropna_df.dropna(subset=['AGE'],axis=0,inplace=True)
```





# We will compare the performance of this model with the imputed and complete models

Now we need to train a linear regression model and predict the 'AGE' values for the boston_impute_df

Should we include the price in the training data for the imputation?

```python
lm_fitting_df = boston_dropna_df.drop('y',axis=1)
lm_fitting_df
```

![截屏2021-01-30 下午9.32.18](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028558.png)

Our target now is the 'AGE' column, we will use the boston_dropna_df as the data to fit, we can use data with missing values to train a model

# Using a separate model

```python
lm_for_impute = LinearRegression() #instatiate
```

```python
lm_for_impute.fit(lm_fitting_df[[x for x in lm_fitting_df.columns if x != 'AGE']],lm_fitting_df['AGE']) #fit
```

```python
boston_impute_df[boston_impute_df['AGE'].isnull()].head()
```

![截屏2021-01-30 下午9.33.48](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028539.png)

```python
lm_for_impute.predict(boston_impute_df.drop(['AGE','y'],axis=1)) 
#this uses the other features to predict 'AGE' with the model
```

![截屏2021-01-30 下午9.34.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028015.png)

```python
boston_impute_df['AGE'][boston_impute_df['AGE'].isnull()] = lm_for_impute.predict(boston_impute_df.drop(['AGE','y'],axis=1))
```

![截屏2021-01-30 下午9.37.56](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028032.png)

```python
boxplot = pd.DataFrame({'imputed': boston_impute_df['AGE'],'full': boston_df['AGE'],'dropped': boston_dropna_df['AGE']})
boxplot.plot(kind='box')
```

![截屏2021-01-30 下午9.38.42](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028353.png)



# Predicting the price with the full data



𝑦𝑖=𝛽0+𝛽1𝑋𝑖+𝜖𝑖

```python
X_train, X_test, y_train, y_test = train_test_split(boston_df.iloc[:,:-1],boston_df.iloc[:,-1:],random_state=111)
```

```python
map(pd.np.shape,[X_train, X_test, y_train, y_test])
```

```python
lm_full = LinearRegression()
```

```python
lm_full.fit(X_train,y_train)
```

```python
print ('r-squared for this model = ',lm_full.score(X_test,y_test))
```

![截屏2021-01-30 下午9.41.45](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028434.png)

# The dropped data

```python
X_train, X_test, y_train, y_test = train_test_split(boston_dropna_df.iloc[:,:-1],boston_dropna_df.iloc[:,-1:],random_state=111)
```

```python
lm_dropped = LinearRegression()
lm_dropped.fit(X_train,y_train)
print ('r-squared for this model = ',lm_dropped.score(X_test,y_test))
```

![截屏2021-01-30 下午9.41.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028518.png)



# The imputed data

```python
X_train, X_test, y_train, y_test = train_test_split(boston_impute_df.iloc[:,:-1],boston_impute_df.iloc[:,-1:],random_state=111)
```

```python
lm_impute = LinearRegression()
lm_impute.fit(X_train,y_train)
print ('r-squared for this model = ',lm_impute.score(X_test,y_test))
```

![截屏2021-01-30 下午9.43.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230028571.png)





