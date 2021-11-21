

## 1. 初识Pandas

```python
# Introduction to Data Wrangling with Pandas: the Python Data Analysis library
import pandas
from pandas import tools
import pandas as pd
import numpy as np
```

```python
df = pd.DataFrame([2,4,6,8])
df

>>
0
0	2
1	4
2	6
3	8
```

```python
df.columns.name = "Index"
df

>>
Index	0
0	2
1	4
2	6
3	8
```

```python
# You can also use pandas to create an series of datetime objects. Let's make one for the week beginning January 25th, 2015:
dates = pd.date_range('20150125', periods=7)
dates

>>
DatetimeIndex(['2015-01-25', '2015-01-26', '2015-01-27', '2015-01-28',
               '2015-01-29', '2015-01-30', '2015-01-31'],
              dtype='datetime64[ns]', freq='D')
```

```python
df = pd.DataFrame(np.random.randn(7,5), index=dates, columns={'Adam','Bob','Carla','Dave','Eve'})
df

>>
Eve	Adam	Dave	Bob	Carla
2015-01-25	-0.594398	0.354574	-1.800184	1.102192	-1.335609
2015-01-26	0.859395	-0.154730	-0.600227	0.486813	-0.987400
2015-01-27	3.055485	-1.191046	-0.545422	-0.558445	0.696324
2015-01-28	-0.839616	-0.065840	-1.552635	-0.083895	0.495785
2015-01-29	0.108804	0.295297	-0.827451	1.499309	2.043015
2015-01-30	-0.325290	-0.308536	0.933446	-1.375990	1.589074
2015-01-31	1.817642	0.041496	-0.359412	-0.539146	0.759547
```

```python
# This comes in particularly handy when working with JSON-like structures.
df2 = pd.DataFrame({ 'A' : np.random.random_sample(4), # 4 random numbers
                     'B' : pd.Timestamp('20130102'), # 4 dates, note pandas autofills
                     'C' : pd.date_range('20150125',periods = 4), # 4 dates in a range
                     'D' : ['a','b','c','d'], # letters
                     'E' : ["cat","dog","mouse","parrot"], # text/string
                     'F' : 'copy'}) # note pandas autofills                  
df2                  

>>
				 A					 B					 C	D	  E		 F
0	0.459838	2013-01-02	2015-01-25	a	cat	copy
1	0.664234	2013-01-02	2015-01-26	b	dog	copy
2	0.075784	2013-01-02	2015-01-27	c	mouse	copy
3	0.305348	2013-01-02	2015-01-28	d	parrot	copy
```

## 2. Exploring the data in a DataFrame

```python
# access the data types of each column in a DataFrame
df2.dtypes

>>
A           float64
B    datetime64[ns]
C    datetime64[ns]
D            object
E            object
F            object
dtype: object
```

```python
# display the index, columns, and the underlying numpy data 
df2.index

>>
RangeIndex(start=0, stop=4, step=1)
```

```python
df2.columns

>>
Index(['A', 'B', 'C', 'D', 'E', 'F'], dtype='object')
```

```python
df2.values

>>
array([[0.4598377526045867, Timestamp('2013-01-02 00:00:00'),
        Timestamp('2015-01-25 00:00:00'), 'a', 'cat', 'copy'],
       [0.6642338855994059, Timestamp('2013-01-02 00:00:00'),
        Timestamp('2015-01-26 00:00:00'), 'b', 'dog', 'copy'],
       [0.07578382085467139, Timestamp('2013-01-02 00:00:00'),
        Timestamp('2015-01-27 00:00:00'), 'c', 'mouse', 'copy'],
       [0.30534817865643427, Timestamp('2013-01-02 00:00:00'),
        Timestamp('2015-01-28 00:00:00'), 'd', 'parrot', 'copy']],
      dtype=object)
```

```python
# To get a quick statistical summary of your data
df2.describe()

>>
	A
count	4.000000
mean	0.376301
std	0.248481
min	0.075784
25%	0.247957
50%	0.382593
75%	0.510937
max	0.664234
```

## 3. Some basic data transformations

```python
# DataFrames have a built-in transpose
df2.T

>>
0	1	2	3
A	0.459838	0.664234	0.0757838	0.305348
B	2013-01-02 00:00:00	2013-01-02 00:00:00	2013-01-02 00:00:00	2013-01-02 00:00:00
C	2015-01-25 00:00:00	2015-01-26 00:00:00	2015-01-27 00:00:00	2015-01-28 00:00:00
D	a	b	c	d
E	cat	dog	mouse	parrot
F	copy	copy	copy	copy
```

```python
# sort a DataFrame along a given data dimension
df.sort_values(by = 'Bob')

>>
date Eve	Adam	Dave	Bob	Carla
2015-01-30	-0.325290	-0.308536	0.933446	-1.375990	1.589074
2015-01-27	3.055485	-1.191046	-0.545422	-0.558445	0.696324
2015-01-31	1.817642	0.041496	-0.359412	-0.539146	0.759547
2015-01-28	-0.839616	-0.065840	-1.552635	-0.083895	0.495785
2015-01-26	0.859395	-0.154730	-0.600227	0.486813	-0.987400
2015-01-25	-0.594398	0.354574	-1.800184	1.102192	-1.335609
2015-01-29	0.108804	0.295297	-0.827451	1.499309	2.043015
```

### 3.1 Selection

```python
df.head()
df.tail(2)
df['Dave']
df[1:3]
df['20150127':'20150129']
df.loc[:,['Dave':'Eve']]
```

### 3.2 Conditional Indexing

```python
# to select all rows that meet some criteria
df[df['Eve'] > 0]

>>

Eve	Adam	Dave	Bob	Carla
2015-01-26	0.859395	-0.154730	-0.600227	0.486813	-0.987400
2015-01-27	3.055485	-1.191046	-0.545422	-0.558445	0.696324
2015-01-29	0.108804	0.295297	-0.827451	1.499309	2.043015
2015-01-31	1.817642	0.041496	-0.359412	-0.539146	0.759547
```

```python
# or to eliminate all negative values
nonneg_only = df[df > 0]
nonneg_only

>>
Eve	Adam	Dave	Bob	Carla
2015-01-25	NaN	0.354574	NaN	1.102192	NaN
2015-01-26	0.859395	NaN	NaN	0.486813	NaN
2015-01-27	3.055485	NaN	NaN	NaN	0.696324
2015-01-28	NaN	NaN	NaN	NaN	0.495785
2015-01-29	0.108804	0.295297	NaN	1.499309	2.043015
2015-01-30	NaN	NaN	0.933446	NaN	1.589074
2015-01-31	1.817642	0.041496	NaN	NaN	0.759547
```

```python
# to drop all the rows with missing values
nonneg_only.dropna()
```

```python
# set them to zero instead
nonneg_only.fillna(value = 0)

>>
	Eve	Adam	Dave	Bob	Carla
2015-01-25	0.000000	0.354574	0.000000	1.102192	0.000000
2015-01-26	0.859395	0.000000	0.000000	0.486813	0.000000
2015-01-27	3.055485	0.000000	0.000000	0.000000	0.696324
2015-01-28	0.000000	0.000000	0.000000	0.000000	0.495785
2015-01-29	0.108804	0.295297	0.000000	1.499309	2.043015
2015-01-30	0.000000	0.000000	0.933446	0.000000	1.589074
2015-01-31	1.817642	0.041496	0.000000	0.000000	0.759547
```

```python
df2 = df.copy()
df2['color']=['blue', 'green','red','blue','green','red','blue']
df2

>>
	Eve	Adam	Dave	Bob	Carla	color
2015-01-25	-0.594398	0.354574	-1.800184	1.102192	-1.335609	blue
2015-01-26	0.859395	-0.154730	-0.600227	0.486813	-0.987400	green
2015-01-27	3.055485	-1.191046	-0.545422	-0.558445	0.696324	red
2015-01-28	-0.839616	-0.065840	-1.552635	-0.083895	0.495785	blue
2015-01-29	0.108804	0.295297	-0.827451	1.499309	2.043015	green
2015-01-30	-0.325290	-0.308536	0.933446	-1.375990	1.589074	red
2015-01-31	1.817642	0.041496	-0.359412	-0.539146	0.759547	blue
```

```python
# Now we can use the isin() function to select only the rows with 'green' or 'blue' in the color column:
df2[(df2['color'] == 'green') | (df2['color'] == 'blue')]

df2[df2['color'].isin(['green','blue'])]

>> 
Eve	Adam	Dave	Bob	Carla	color
2015-01-25	-0.594398	0.354574	-1.800184	1.102192	-1.335609	blue
2015-01-26	0.859395	-0.154730	-0.600227	0.486813	-0.987400	green
2015-01-28	-0.839616	-0.065840	-1.552635	-0.083895	0.495785	blue
2015-01-29	0.108804	0.295297	-0.827451	1.499309	2.043015	green
2015-01-31	1.817642	0.041496	-0.359412	-0.539146	0.759547	blue
```

### 3.3 Basic Math

```python
df.mean()
df.median()
```

```python
'''
You can also use the apply() function to evaluate functions to the data.
'''

# perform a cumulative summation
df.apply(np.cumsum)
# finding the spread (max value - min value)
df.apply(lambda x: x.max() - x.min())
```

### 3.4 Combining DataFrames

```python
# using simple concatenation (provided they have the same columns)
frame_one = pd.DataFrame(np.random.randn(5, 4))
frame_two = pd.DataFrame(np.random.randn(5, 4))
pd.concat([frame_one, frame_two])

>>
0	1	2	3
0	0.098458	0.352647	-0.226132	-0.212292
1	-0.802403	0.812487	0.051910	-0.419202
2	0.382539	-0.691836	-1.897864	-0.352557
3	-0.317451	0.342897	0.377241	0.742086
4	-0.490925	0.049263	-0.235367	0.034409
0	0.385179	0.491085	-0.805751	0.095701
1	-1.483061	-0.103381	-0.294970	-0.795574
2	-2.229632	-1.255831	1.186378	0.343793
3	0.890929	0.402436	0.496771	-0.347197
4	-0.815891	0.990842	-0.543900	0.456994
```

```python
'''
If your DataFrames do not have an identical structure, but do share a common key, you can also perform a SQL-style join using the merge() function
'''

left = pd.DataFrame({'key': ['foo','far'], 'lval':[1,2]})
right = pd.DataFrame({'key':['foo', 'foo', 'bar'], 'rval':[3,4,5]})

pd.merge(left,right, on = 'key')

>>
	key	lval	rval
0	foo	1	3
1	foo	1	4
```

### 3.5 Grouping

```python
'''
Sometimes when working with multivariate data, it's helpful to be able to condense the data along a certain dimension in order to perform a calculation for efficiently. Let's start by creating a messy DataFrame:
'''
foo_bar = pd.DataFrame({'A' : ['foo', 'bar', 'foo', 'bar','foo', 'bar', 'foo', 'foo'],
                            'B' : ['one', 'one', 'two', 'three', 'two', 'two', 'one', 'three'],
                            'C' : np.random.randn(8),
                            'D' : np.random.randn(8)})

foo_bar

>>

A	B	C	D
0	foo	one	0.745589	0.889573
1	bar	one	0.522196	1.166998
2	foo	two	0.094883	-0.152321
3	bar	three	-0.666723	1.947593
4	foo	two	-0.758670	-0.190475
5	bar	two	0.261167	-0.976714
6	foo	one	-0.251056	-0.418574
7	foo	three	0.426163	0.330458
```

```python
'''
Now let's group by column A, and sum along the other columns:
'''
foo_bar.groupby('A').sum()

>>
		C	D
A		
bar	0.116641	2.137877
foo	0.256908	0.458662
```

```python
'''
Note that column B was dropped, because the summation operator doesn't make sense on strings. However, if we wanted to retain that information, we could perform the same operation using a hierarchical index:
'''
grouped = foo_bar.groupby(['A','B']).sum()
grouped
```

![截屏2021-01-25 上午10.04.56](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8A%E5%8D%8810.04.56.png?token=AWS37JLBH3COPW53GLRD2GTBTH4P2)

### 3.6 Time Series

pandas has simple, powerful, and efficient functionality for performing resampling operations during frequency conversion (for example, converting secondly data into minutely data). Firse, let's create an array of dateTime objects at a frequency of 1 second:

```python
rng = pd.date_range('1/1/2015', periods = 100, freq = 'S')
rng[1:5]
```

![截屏2021-01-25 上午10.07.40](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8A%E5%8D%8810.07.40.png?token=AWS37JI37UPKRPNVNMTPS6LBTH4SM)

Now we'll use that to greate a time series, assigning a random integer to each element of the range:

```python
time_series = pd.Series(np.random.randint(0, 500, len(rng)), index = rng)
time_series.head()
```

![截屏2021-01-25 上午10.17.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8A%E5%8D%8810.17.05.png?token=AWS37JOBAHBANHTEBZCCSO3BTH436)

Next, we'll resample the data by binning the one-second raws into minutes (and summing the associated values):

```python
time_series.resample('1Min').sum()
```

![截屏2021-01-25 上午10.21.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8A%E5%8D%8810.21.36.png?token=AWS37JPXPQNV5HXTEW373H3BTH46A)

### 3.7 Reading/Writing to files

```python
# Writing to a file is straightforward:
foo_bar.to_csv('foo.csv')
```

```python
# As is reading
new_frame = pd.read_csv('foo.csv')
```

```python
new_frame.head()
```

![截屏2021-01-25 下午12.15.32](https://raw.githubusercontent.com/Peiyang-Felix/PicGo/Typora-DataContent/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%8812.15.32.png)

```python
new_frame.iloc[:,1:].head()
```

![截屏2021-01-25 下午12.15.57](https://raw.githubusercontent.com/Peiyang-Felix/PicGo/Typora-DataContent/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%8812.15.57.png)

```python
fixed_df = pd.read_csv('bikes.csv',
                       sep=';',
                       encoding='latin1',
                       parse_dates=['Date'],
                       dayfirst = True,
                       index_col = 'Date'
                      )
fixed_df.head()
```

## 4. Scraping data from the web

Many of you will probably be interested in scraping data from the web for your projects. For example, what if we were interested in working with some historical Canadian weather data? Well, we can get that from: [http://climate.weather.gc.ca](http://climate.weather.gc.ca/) using their API.

```python
url_template = "http://climate.weather.gc.ca/climate_data/bulk_data_e.html?format=csv&stationID=5415&Year={year}&Month={month}&timeframe=1&submit=Download+Data"
```

Note that we've requested the data be returned as a csv, and that we're going to supply the month and year as inputs when we fire off the query. To get the data for March 2013, we need to format it with month=3, year=2012:

```python
url = url_template.format(month=3, year=2012)
```

This is great! We can just use the same read_csv function as before, and just give it a URL as a filename. Awesome.

Upon inspection, we find out that there are 16 rows of metadata at the top of this CSV, but pandas knows CSVs are weird, so there's a skiprows options. We parse the dates again, and set 'Date/Time' to be the index column. Here's the resulting dataframe.

```python
weather_mar2012 = pd.read_csv(url,
                              skiprows=16,
                              index_col='Date/Time',
                              parse_dates=True,
                              encoding='latin1'
                             )
```

```python
weather_mar2012 = weather_mar2012.dropna(axis=1, how='any')
```

```python
weather_mar2012 = weather_mar2012.drop(['Year', 'Month', 'Day', 'Time', 'Data Quality'], axis=1)

```

Great! Now let's figure out how to download the whole year? It would be nice if we could just send that as a single request, but like many APIs this one is limited to prevent people from hogging bandwidth. No problem: we can write a function!

```python
def download_weather_month(year, month):
    url = url_template.format(year=year, month=month)
    weather_data = pd.read_csv(url, skiprows=16, index_col='Date/Time', parse_dates=True)
    weather_data = weather_data.dropna(axis=1)
    weather_data.columns = [col.replace('\xb0', '') for col in weather_data.columns]
    weather_data = weather_data.drop(['Year', 'Day', 'Month', 'Time', 'Data Quality'], axis=1)
    return weather_data
```

Now to test that this function does the right thing:

```python
download_weather_month(2012, 1).head()
```

![截屏2021-01-25 下午2.01.38](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%882.01.38.png?token=AWS37JKE2CTITSI2QGAGUADBTH5DS)

Woohoo! Now we can iteratively request all the months using a single line. This will take a little while to run.

```python
data_by_month = [download_weather_month(2012, i) for i in range(1, 12)]
```

Once that's done, it's easy to concatenate all the dataframes together into one big dataframe using 𝚙𝚊𝚗𝚍𝚊𝚜.𝚌𝚘𝚗𝚌𝚊𝚝()pandas.concat(). And now we have the whole year's data!

```python
weather_2012 = pd.concat(data_by_month)
```

This thing is long, so instead of printing out the whole thing, I'm just going to print a quick summary of the 𝙳𝚊𝚝𝚊𝙵𝚛𝚊𝚖𝚎DataFrame by calling .𝚒𝚗𝚏𝚘().info():

```python
weather_2012.info()
```

![截屏2021-01-25 下午2.03.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%882.03.55.png?token=AWS37JMULRRBTH7O76DD4MTBTH5DW)

And a quick reminder, if we wanted to save that data to a file:

```python
weather_2012.to_csv('weather_2012.csv')
```



## 5. Plot data

And finally, something you should do early on in the wrangling process, plot data:

```python
# plot that data
import matplotlib.pyplot as plt 
# so now 'plt' means matplotlib.pyplot
plt.plot(df)
```

![截屏2021-01-25 下午2.08.14](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%882.08.14.png?token=AWS37JLDJNCGKZGYZK6IHS3BTH5GQ)

```python
# nothing to see... in iPython you need to specify where the chart will display, usually it's in a new window
# to see them 'inline' use:
%matplotlib inline
```



## One more example

We are interested in getting State and Territory information from Wikipedia, however we do not want to copy and paste the table : )

Here is the URL https://en.wikipedia.org/wiki/States_and_territories_of_Australia (Accessed on 10 July 2016)

We need two libraries to do the task:

Check documentations here:

- [urllib](https://docs.python.org/2/library/urllib.html)
- [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)

```python
import sys
if sys.version_info[0] == 3:
    from urllib.request import urlopen
else:
    from urllib import urlopen
from bs4 import BeautifulSoup
```

We first save the link in wiki

```python
wiki = "https://en.wikipedia.org/wiki/States_and_territories_of_Australia"
```

Then use ulropen to open the page.

If you get "SSL: CERTIFICATE_VERIFY_FAILED", what you need to do is find where "Install Certificates.command" file is, and click it to upgrade the certificate. Then, you should be able to solve the problem.

```python
page = urlopen(wiki)

if sys.version_info[0] == 3:
		page = page.read()
```

You will meet BeautifulSoup later in this subject, so don't worry if you feel uncomfortable with it now. You can always revisit.

We begin by reading in the source code and creating a Beautiful Soup object with the BeautifulSoup function.

```python
soup = BeautifulSoup(page, "html")
print(soup.prettify())
```

```python
soup.title.string
```

```python
all_tables = soup.findAll('table')
print(all_tables)
```

```python
right_table=soup.find('table', class_='wikitable sortable')
print (right_table)
```

```python
head_row = right_table.find('tr')
print (head_row)
```





```python
header_list = []
headers = head_row.findAll('th')
for header in headers:
    #print header.find(text = True)
    header_list.append(header.find(text = True))
header_list
```

```python
flag=[]
state=[]
abbrev = []
ISO = []
Postal =[]
Type = []
Capital = []
population = []
Area = []
for row in right_table.findAll("tr"):
    cells = row.findAll('td')
    if len(cells) > 0 and len(cells) == 9:
        flag.append(cells[0].find(text=True))
        state.append(cells[1].find(text=True))
        abbrev.append(cells[2].find(text=True))
        ISO.append(cells[3].find(text=True))
        Postal.append(cells[4].find(text=True))
        Type.append(cells[5].find(text=True))
        Capital.append(cells[6].find(text=True))
        population.append(cells[7].find(text=True))
        Area.append(cells[8].find(text=True))
```

```python
df_au = pd.DataFrame()
df_au[header_list[0]] = flag
df_au[header_list[1]] = state
df_au[header_list[2]]=abbrev
df_au[header_list[3]]=ISO
df_au[header_list[4]]=Postal
df_au[header_list[5]]=Type
df_au[header_list[6]]=Capital
df_au[header_list[7]]=population
df_au[header_list[8]]=Area
```

```python
df_au
```

![截屏2021-01-25 下午2.43.25](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%882.43.25.png?token=AWS37JO7QZ3ZQPO6FXW4B3DBTH5IK)

























