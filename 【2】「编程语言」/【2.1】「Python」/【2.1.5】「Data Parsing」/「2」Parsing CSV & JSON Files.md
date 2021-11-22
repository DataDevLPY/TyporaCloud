- **CSV**: Comma Separated Values
- **JSON**: JavaScript Object Notation



## Importing CSV data

Importing CSV files with Pandas [read_csv()](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_csv.html) function and converting the data into a form Python can understand is simple. It only takes a couple of lines of code. The imported data will be stored in Pandas DataFrame.

```python
import pandas as pd
csvdf = pd.read_csv('./Melbourne_bike_share.csv', index_col = "ID")
```

Or you can use the [read_table()](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.read_table.html) function

```python
csvdf_1 = pd.read_table('./Melbourne_bike_share.csv', sep = ',')
```

### Manipulating the Data

```python
import re
str1 = csvdf['Coordinates'].iloc[0] # csvdf.Coordinates
re.findall(r"-?\d+\.?\d*", str1)
```

![截屏2021-01-28 上午8.35.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230021227.png)

The latitude and longitude in the first coordinate have been successfully extracted. Next, we are going to apply the extracting process to every coordinate in the DataFrame. There are multiple ways of doing that. The most straightforward way is to write a FOR loop to iterate over all the coordinates, and apply the above scripts to each individual coordinate. Two Pandas Series can be then used to store latitudes and longitudes. However, we are going to show you how to use some advanced Python programming functionality.

Pandas Series class implements an [`apply()`](http://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.apply.html) method that applies a given function to all values in a Series object, and returns a new one. Please note that this function can only works on single values. To apply str.split() to every coordinate and get latitudes and longitudes, you can use the following two lines of code:

```python
csvdf['lat'] = csvdf['Coordinates'].apply(lambda x: x.split(', ')[0])
csvdf['lon'] = csvdf['Coordinates'].apply(lambda x: x.split(', ')[1])
csvdf['lat'] = csvdf['lat'].apply(lambda x: x.lstrip('('))
csvdf['lon'] = csvdf['lon'].apply(lambda x: x.rstrip(')'))
csvdf.drop('Coordinates', 1, inplace = True)
```

![截屏2021-01-28 上午9.04.15](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230021391.png)

### Convert types

```python
csvdf = csvdf.apply(pd.to_numeric, errors = 'ignore') 
csvdf['UploadDate'] = pd.to_datetime(csvdf['UploadDate'])
```





