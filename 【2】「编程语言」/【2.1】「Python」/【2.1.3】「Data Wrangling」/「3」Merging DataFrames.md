

# Merging DataFrames

This part discusses the basic and essential practical steps for integrating data from various sources.

Dealing with data from different sources requires essentially the integration between these sources to have a full information view. For example, if you collect data about house pricing separately in each suburb, you might need to "concatenate" data together to get a view of the house prices all over Victoria and even Australia. However, each data collection might have different attributes. How to merge them together to get a complete yet precise (with no duplication) presentation of the whole collection? Consider another example for sales department, when you have customer details in one table and product details in another table. How can we use Python (pandas library) to execute the join between the two DataFrame and manage the many to many relationship. This is exactly about implementing database techniques for merging tables using python on a general data.

Panda library has offered methods to manage the data integration task. In the following, we will discuss each method with examples.

There are four ways to merge/combine between different DataFrames in pandas:

- concatenating,
- appending,
- merging, and
- joining.

Each has its own use cases and best practice. We present concatenating and appending in this tutorial, the other two methods will be discussed in the next tutorial (week 11B). In all of these methods, we assume data is fetched or scrapped from the web (as explained in week 10) or it is stored locally on the machine. We also assume that the data from each source is stored in a DataFrame structure. We start first with the easiest way for merging DataFrame with concatenating.

## Methods for integrating data with Pandas:

## 1. Concatenating:

This refers to gluing together data from different DataFrames by stacking them either vertically or side by side. Consider the following example.

#### Create a dataframe

```python
#Import module
import pandas as pd

# Create a dataframe
df1= pd.DataFrame({'Student_ID': ['1', '2', '3', '4'],
                      'First_Name': ['A1','A2','A3','A4'],
                    'Last_Name': ['B1', 'B2', 'B3', 'B4']},
                  index=[1,2,3,4])
df1
```

![??????2021-01-31 ??????4.04.19](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017692.png?token=AWS37JKLQ2MIKA2SYIULCLTBTJYUG)

```python
df = pd.DataFrame([{'Student':'1','team':2},{'Student':'2','team':2}])
df
```

![??????2021-01-31 ??????4.04.58](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017475.png?token=AWS37JI5Z5BDWNHJTNOIR4TBTJYUI)

#### Create a second dataframe

```python
df2= pd.DataFrame({'Student_ID': ['4', '5', '6', '7','8'],
                      'First_Name': ['A4','A5','A6','A7','A8'],
                    'Last_Name': ['B4', 'B5', 'B6', 'B7','b8']},
                 index= [4,5,6,7,8])
df2
```

![??????2021-01-31 ??????4.06.17](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017766.png?token=AWS37JJSB45AA3X62NWUGFLBTJYUS)

#### Create a third dataframe

```python
df3= pd.DataFrame({'Student_ID': ['9', '10', '11', '12'],
                    'Last_Name': ['B9', 'B10', 'B11', 'B12'],
                  'address':['AD9','AD10','AD11','AD12']})
df3
```

![??????2021-01-31 ??????4.10.23](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017499.png?token=AWS37JOI4THAM3JMEOI2N5TBTJYUY)

#### Join the two dataframes along rows

```python
concat_1_2= pd.concat([df1,df2])
concat_1_2
```

![??????2021-01-31 ??????4.12.26](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017564.png?token=AWS37JI7XE7UGCITSBYTALDBTJYU6)

```python
concat_!_2 = pd.concat([df1,df2],axis = 0)
```



![??????2021-01-31 ??????4.13.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017221.png?token=AWS37JNITWQNHDODQCS6243BTJYVE)

```python
concat_1_2_3= pd.concat([df1,df2,df3])
concat_1_2_3
```

![??????2021-01-31 ??????4.14.51](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017353.png?token=AWS37JJTXVRV3WKYELB2RGLBTJYVK)

As explained in this example, concatenation glues different DataFrames together without considering the index of any or duplication that might cause. When concatenating, you need to make sure that all DataFrames have the same headers. Otherwise, the values of any column that does not exist in the union of headers of concatenated DataFrame will be replaced with NAN values, as shown when concatenated df3 with df1 and df2.

Note also, concatenation assigns the default index when the index was not provided in the original DataFrame (as in df3).
"ignore_index" method is used in case you want to concatenate the DataFrames and ignore the indexes (because they are not meaningful)

```python
result = pd.concat([df1, df3], ignore_index=True)
print(df1)
print(df3)
print(result)
```

![??????2021-01-31 ??????4.16.00](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017708.png?token=AWS37JIJAZHGPVEU6AGGMWDBTJYWA)

The concatenation is very useful when you have data (with the same attributes) coming from different sources i.e., house prices collected from each suburb. So we glue them together to get a view for house prices all over victoria. We can also add another key column to indicate the source of each chunk of data in a hierarchical way. The example below explains this as follows:

```python
concat_1_2= pd.concat([df1,df2], keys=['source1','source2'])
print(concat_1_2)
```

![??????2021-01-31 ??????4.16.58](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017085.png?token=AWS37JJHDHZZOJTYEQP7DWTBTJYWE)

An important use case for concatenating is when different perspectives of the same data are collected, so we need to concatenate all the information together to get all the details in one DataFrame. The following example illustrates the concatenation of DataFrame represents landlord profile with another DataFrame for the property details. The concatenation in this case has to be implemented along the horizontal axis (axis=1) rather than the vertical one, while the default concatenation axis is 0 (vertical).

```python
df4=pd.DataFrame({'Student_ID': ['1', '2', '3', '4'],
                      'Addres': ['AD1','AD2','AD3','AD4'],
                    'year': ['Y1', 'Y2', 'Y3', 'Y4']},
                 index={1,2,3,4}
                 )
concat_1_4_horizontal= pd.concat([df1,df4],axis=1)
print(concat_1_4_horizontal)
concat_2_4_horizontal= pd.concat([df2,df4],axis=1)
print(concat_2_4_horizontal)
```

![??????2021-01-31 ??????4.19.23](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220017436.png?token=AWS37JJNV7EVZZDRFWS22WLBTJYXI)

Concatenating df2 and df4 results in many NAN values because of the non-overlapping in index. Therefore, joining the DataFrames is very useful in this case. Concatenation gives you the three options to handle the other axes too (apart from the one we concatenate on) using outer or inner options or using a specific index. The outer option is the default gets the union of data and grantees zero loss. The previous example shows the outer join of df2 and df4.  On the other hand, inner gets the intersection between the two DataFrames. Last but not least, the ???join_axes??? argument is used for joining with a specific index. 

```python
joint_result= pd.concat([df2, df4], axis=1, join='inner')
print(joint_result)
```

![??????2021-01-31 ??????4.45.26](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220018339.png?token=AWS37JLJTQWXPP4V2MCVYXLBTJYXO)

```python
merge_2_4 = pd.merge(df1 , df2, how = 'left',left_index = True, right_index = True)
merge_2_4
```

![??????2021-01-31 ??????4.45.41](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220018805.png?token=AWS37JKX6JC27QF5WF4ZH6DBTJYXU)



## 2. Append:

Append method in Series and DataFrames is a shortcut of concatenating. It is easy to use but not efficient in terms of performance. When appending a DataFrame, the original DataFrame remains in memory and a new appended one is created. In the appending process, the indexes must be disjoint but the columns do not need to be.

```python
appended_df= df1.append([df2,df3])
print(appended_df)
```

![??????2021-01-31 ??????4.46.46](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220018979.png?token=AWS37JJTEODNF26ISXX4ALDBTJYYA)



## 3. Merge:

Concat and append have limited capabilities in joining tables through keys and considering the inter-relationship between tables. Merge and join on the other hand combine DataFrames based on a key. According to concepts of relational databases like SQL, there are three types of relationships between tables:

1. **One-to-one:** When each primary key value relates to only one (or no) record in the related table.
2. **One-to-many:** When the primary key table contains only one record that relates to none, one, or many records in the related table.
3. **Many-to-many:** When each record in both tables can relate to any number of records (or no records) in the other table.

We will discuss in the following how merge manages the three types of relationships. The following is an example of using merge for one-to-many relationship between table respresnts customer details and shopping history for each customer.



```python
import pandas as pd
customers= pd.DataFrame({'Customer_ID': ['1', '2', '3', '4'],
                      'First_Name': ['A1','A2','A3','A4'],
                    'Last_Name': ['B1', 'B2', 'B3', 'B4']})
shopping_history= pd.DataFrame({'Customer_ID': ['1', '1', '1', '4','5'],
                            'Product_ID':['100','200','300','400','500'],
                      'product': ['Oil','Sugar','Tea','Milk','Eggs']})
merged_df= pd.merge(customers,shopping_history)
print(customers)
print(shopping_history)
print(merged_df)
```

![??????2021-01-31 ??????5.03.22](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220019387.png?token=AWS37JL2MN3C75HRZGPOSFDBTJY4E)

### Try to change Customer_ID to another name!

```python
merged_df= pd.merge(customers,shopping_history, on="Product_ID")
print (merged_df)
```

![??????2021-01-31 ??????5.05.51](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220019551.png?token=AWS37JPAVDW2RLQTBU5KQN3BTJY4I)

## How to merge:

When there are no common columns between the DataFrames, you need to specify the key to merge on. Use on, left_on and right_on attributes to define the key in DataFrames.

```python
customers2= pd.DataFrame({'CID': ['1', '2', '3', '4'],
                      'First_Name': ['A1','A2','A3','A4'],
                    'Last_Name': ['B1', 'B2', 'B3', 'B4']})
merged_onkey= pd.merge(customers2,shopping_history,left_on='CID',right_on='Customer_ID')
print (customers2) 
print (shopping_history)
print (merged_onkey)
```

![??????2021-01-31 ??????5.07.15](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220019002.png?token=AWS37JIV5G7RDAMZ7RAH5Y3BTJY4O)

```python
print (customers)
print (shopping_history)
merged_outer= pd.merge(customers,shopping_history, how='outer')
print (merged_outer)
merged_left= pd.merge(customers,shopping_history, how='left')
print (merged_left)
merged_right= pd.merge(shopping_history,customers, how='right')
print (merged_right)
```

![??????2021-01-31 ??????5.07.45](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220019519.png?token=AWS37JJSL5CWZT2SGDXHXGLBTJY44)

#### Many-to-many merge:

A more complicated relationship to manage using merge method is the many-to-many. Consider the example of customers and products. The customer DataFrame contains information about customers, while products has details about differnt grocery items. One customer can buy non or many products, and any product can be purchased by non or many customers. Pandas manage this relationship using merge method. Like SQL's JOIN clause, pandas.merge allows two DataFrames to be joined on one or more keys. The function provides a series of parameters (on, left_on, right_on, left_index, right_index) allowing you to specify the columns or indexes on which to join. In version 0.17.0. Pandas added the argument indicator. If True, a Categorical-type column called _merge will be added to the output object that takes on values. Here is a simple example.

```python
df1 = pd.DataFrame({'key': ['a', 'a', 'b', 'c', 'a', 'b','c'], 'd1': range(7)})
df2 = pd.DataFrame({'key': ['d', 'd', 'b', 'b', 'b', 'a'], 'd1': range(6)})
print (df1)
print (df2)
merge= pd.merge(df1,df2, how='outer',indicator='True')
print (merge)
```

![??????2021-01-31 ??????5.16.53](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220019847.png?token=AWS37JOZIGJ6MTSUI2N2M4TBTJY5Q)

```python
print (df1)
print (df2)
merge= pd.merge(df1,df2, how='outer',indicator='True', on='d1')
print (merge)
```

![??????2021-01-31 ??????5.18.35](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220019113.png?token=AWS37JIM4L6NNX6H3MTQLW3BTJY5W)

In cases that there is a meaningful index in one of the columns, this index can replace the original DataFrame index. By default, set index returns a new DataFrame, so you will have to specify if you would like the changes to occur in place.

## 4. Join:

```python
customers.set_index('Customer_ID', inplace=True)
shopping_history.set_index('Customer_ID', inplace=True)
print (customers)
print (shopping_history)
```

![??????2021-01-31 ??????5.19.59](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220019469.png?token=AWS37JM74TZ5UATKSURLPN3BTJY6C)



The DataFrame also has a convenient join method for merging on the index. This is used when you have objects with similar row labels, but different columns.

```python
joined= customers.join(shopping_history,how="outer")
print (customers)
print (shopping_history)
print (joined)
```

## * Combining data with overlap:

We use this method when we want to ???patch??? values in one object from values for matching indices in the other. Note that this method only takes values from the right DataFrame if they are missing in the left DataFrame. Consider the following example:

```python
import numpy as np
data1= pd.DataFrame([[1,3,np.nan],[np.nan,10,np.nan],[np.nan,5,3]])
data2= pd.DataFrame([[10,np.nan,4],[np.nan,5,3],[2,4,np.nan]])
print (data1)
print (data2)
data= data1.combine_first(data2)
print (data)
```

![??????2021-01-31 ??????5.24.22](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220019025.png?token=AWS37JJ7UUXTUPSIZVN353TBTJY6Q)

## Basic data Reshaping:

### Hierarchical indexing : Stack and unstack

Hierarchical indexing provides a more structure way of presenting tabular data. There are two main methods for pivoting data with Hierarchical indexing.

- stack: this ???rotates??? or pivots from the columns in the data to the rows
- unstack: this pivots from the rows into the columns



```python
import pandas as pd
import numpy as np
tuples = list(zip(*[['L1_A', 'L1_A', 'L1_B', 'L1_B','L1_C', 'L1_C', 'L1_D', 'L1_D'], ['L2_1', 'L2_2', 'L2_1','L2_2','L2_1', 'L2_2', 'L2_1','L2_2']]))
index = pd.MultiIndex.from_tuples(tuples, names=['first', 'second'])
data = pd.DataFrame(np.random.randn(8, 2), index=index, columns=['A', 'B'])
print (data)
result=data.stack()
print (result)
```

![??????2021-01-31 ??????5.26.35](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220020610.png?token=AWS37JPN2J6AMUCOI5EQ2OLBTJY7A)

![??????2021-01-31 ??????5.27.10](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220020968.png?token=AWS37JP3HSB4OGPTW2ULTBTBTJY7E)

![??????2021-01-31 ??????5.27.24](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220020736.png?token=AWS37JPBNVKUKPOJUTZZ4TDBTJY7O)

![??????2021-01-31 ??????5.28.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220020095.png?token=AWS37JK24GZPDRV2XLQESW3BTJY7Q)

![??????2021-01-31 ??????5.28.26](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220020976.png?token=AWS37JNZMQGQTJ6IS3DCFDTBTJY7U)

```python
joined= customers.join(shopping_history, how= 'outer')
print (shopping_history)
print (customers)
print (joined)
```

![??????2021-01-31 ??????5.28.50](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220020817.png?token=AWS37JJM5ZNL5I4NPTDPNUTBTJZAC)

### Duplication:

The DataFrame method 'duplicated' returns a Boolean Series indicating whether each row is a duplicate or not:

```python
data = pd.DataFrame({'k1': ['Milk'] * 3 + ['Cheese'] * 4,  'k2': [1, 1, 2, 3, 3, 4, 4]})
print (data)
data.duplicated()
cleandata= data.drop_duplicates()
print (cleandata)
```

![??????2021-01-31 ??????5.30.10](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220020010.png?token=AWS37JLNITIA4UX2CNFHMPDBTJZAQ)



As you may have noticed, applying drop_duplicates on the whole DataFrame considrs all othe columns together. We can alternatively specify which column we want to capture the duplication at.

```python
v1= data.drop_duplicates(['k1'])
print (v1)
v2=  data.drop_duplicates(['k2'])
print (v2)
```

![??????2021-01-31 ??????5.31.09](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220020565.png?token=AWS37JLPUPC35GTNNEYN6JDBTJZBU)

### Renaming axis indexes:

Pandas enable modifying the current attribute name using map or renaming methods. Example is as follows:

```python
idf= pd.DataFrame(np.arange(12).reshape((3, 4)), 
                  index=['nsw', 'vic', 'tasmania'],
                  columns=['day1', 'day2', 'day3', 'day4'])
print (idf)
idf.index.map(str.upper)
idf.rename(index=str.upper, columns=str.upper)
```

![??????2021-01-31 ??????5.33.47](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220020226.png?token=AWS37JO3ETR5Y4L2SYK6DBDBTJZCK)