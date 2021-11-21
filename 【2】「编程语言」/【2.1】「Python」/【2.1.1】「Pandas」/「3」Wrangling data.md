# Part 1

## Step 1 Read data

```python
df = pd.read_csv('AirCrashes.csv', names = ['split','split1'])
```

## Step 2 Extract index(s)

### Extract flight information

```python
# now extract flights based on one of the suggestions from DataWrangler 
df['split'].str.extract("Incident(.*)involving")
# treat the 'split' column as a str, then 
# use the extract method on the str
```

![截屏2021-01-25 下午4.41.52](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%884.41.52.png?token=AWS37JIGAPBYOX6YSLFMHBDBTH6GK)

### But we want planes in a new column, we can create one called 'extract' like this:

```python
df.insert(1, 'extract', df['split'].str.extract("Incident (.*) involving"), expand=True)
```

![截屏2021-01-25 下午4.47.51](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%884.47.51.png?token=AWS37JI5GJGV4KTFFWAJV5TBTH6GM)

```python
# and repeat to get the aircraft type 
df.insert(1, 'extract1', df['split'].str.extract(" a (.*) in ",expand= True))
# and df.head(20) to confirm
df.head(5)
```

![截屏2021-01-25 下午4.51.58](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-25 下午4.51.58.png)

## Step 3 'Fill down'

```python
df = df.fillna(method = 'pad') # magic, take the previous value (not NaN) and fill down

# http://pandas.pydata.org/pandas-docs/stable/missing_data.html#filling-missing-values-fillna
```

![截屏2021-01-25 下午4.54.43](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%884.54.43.png?token=AWS37JJNRZOGYENNWSTOO3TBTH6HC)

## Step 4 Remove the index row

We need to delete all the 'incident' rows, they have served their purpose and are now redundant.

```python
# use str.contain function to get the row index.
# keep everything that doesn't have "Incident" in it 
df = df[df['split'].str.contains("Incident") == False]
```

![截屏2021-01-25 下午4.58.00](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%884.58.00.png?token=AWS37JIYOXN7JW7MSLWCTWDBTH6HU)

## Now to 'unfold', there are several options

```python
data = df.pivot(index = 'extract', columns = 'split', values = 'split1') 
```

```python
data.head() # 58 records, good, but lost plane type, bad
# where's 'extract1' - can we have multiple indexes or have to put that data back in?
```

![截屏2021-01-25 下午5.07.03](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%885.07.03.png?token=AWS37JMQ6552ALQAVKHQJXLBTH6IC)

```python
# solution: make the function a copy, x = x
# data = pd.pivot_table(df, index=["extract","extract1"], columns = 'split', values = 'split1', aggfunc = lambda x: x)   
# or 
data = pd.pivot_table(df, index=["extract","extract1"], columns = 'split', values = 'split1', aggfunc = 'max') 
```

![截屏2021-01-25 下午5.09.45](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%885.09.45.png?token=AWS37JKGZQC5OS45S3TSXY3BTH6IW)



```python
data.reset_index()
```

![截屏2021-01-25 下午5.11.19](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%885.11.19.png?token=AWS37JOGKCPCHIECDGONXL3BTH6JG)





# Another way to explore... plot that data:

```python
%matplotlib inline
# notebook majik to display plots in the notebook

data[['Crew','Passengers']].plot(x='Crew', y='Passengers', kind='scatter')
```

![截屏2021-01-25 下午5.15.09](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%885.15.09.png?token=AWS37JJO6U5KUD4XCYBHEOTBTH6KU)

## Plotting non-numeric data

```python
data['Phase'].value_counts().plot(kind='bar')
```

![截屏2021-01-25 下午5.20.37](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%885.20.37.png?token=AWS37JNQOQNO33QIXVBYQCDBTH6LG)



# Part 2 alternative solution

Step 1. Inspect raw data
Step 2. Tidy up the raw data file
Step 3. Read data to a pandas dataframe
Step 4. Transpose each observation from long to wide and append all observations to a master dataframe



```python
import pandas as pd
# to make the code compatable with python 3
from __future__ import print_function, division
```



## Step 1 inspect raw data

A lot can be learned from looking at the raw data. As the file is not large, we will first read the data file and have a quick look.

```python
with open('AirCrashes.csv','r') as infile:
  	text = infile.read()
text
```

![截屏2021-01-25 下午5.30.43](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%885.30.43.png?token=AWS37JLPM4HDD54T7G437PLBTH6LQ)

```python
print(text)
```

![截屏2021-01-25 下午5.31.31](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%885.31.31.png?token=AWS37JIXHF7BCX272IB3V53BTH6LU)

## Step 2 Tidy up the raw data file

We will make changes to the raw data file and save it to a file (readable via pandas) by read each line and:

1. add comma after "Incident",
2. remove blank lines, and
3. save the file to 'AirCrashes_fixed.csv'

```python
with open('AirCrashes.csv','r') as infile:
    with open('AirCrashes_fixed.csv','w') as outfile: # somewhere to write output to.
        text = infile.read()
        text = text.split('\n') # create a list to iterate over
        for line in text:
            if line == '': continue # deal with the blank lines
            if line.startswith('Incident'):
                line = line.replace('Incident','Incident,') # add the comma after 'Incident' column header
            print(line + '\n\r',file=outfile) #create a new csv with newline and carrige return characters

```

## Step 3 Read data to a pandas dataframe

Now we import the dataset to pandas.

```python
pd.read_csv('AirCrashes_fixed.csv', header = None).head()
```

![截屏2021-01-25 下午5.39.28](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/%E6%88%AA%E5%B1%8F2021-01-25%20%E4%B8%8B%E5%8D%885.39.28.png?token=AWS37JPTSPO2R33GQZDCN4DBTH6MW)

## Step 4 Transpose each observation from long to wide and append all observations

We achieve this by

1. divide the dataset to chunks when using read.csv (chunksize is 16 rows);
2. transpose data from long to wide for each chunk and then
3. append each transposed line to the "Master" dataset

We have tested with only a few observations, which worked. Here are the final code:

```python
# read data in to chunks
chunky_data = pd.read_csv('AirCrashes_fixed.csv',header = None,iterator=True,chunksize=16)

# define an empty dataframe 

if sys.version_info[0] == 3:
    master = pd.DataFrame(columns = range(16))
else:
    master = pd.DataFrame(columns = xrange(16))
# loop through each chunk in chunky_data
for chunk in chunky_data:
    # transponse chunk and save the second row (the row containing values not header) to "row"
    row = chunk.T.iloc[1,:]
    # append a "row" to the master dataframe
    master = master.append(pd.Series(row.get_values()),ignore_index=True)
# print master
master
```

![截屏2021-01-25 下午5.44.51](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/截屏2021-01-25 下午5.44.51.png?token=AWS37JKLHY57EOVDWIXUG73BTH6NC)

```python
column_headers = data[0].unique().tolist()  # Get the data again
master.columns = column_headers # We supply it with a list of the same length
```









