# Parsing Excel Files

There are many ways of manipulating data stored in Excel spreadsheets. For instance, "[Working with Excel Files in Python](http://www.python-excel.org/)" contains pointers to the best information available about working with Excel files in Python. The website lists the following Python packages that deal with Excel:

- `openpyxl`: Reads/writes Excel 2010 xlsx/xlsm/xltx/xltm files.
- `xlsxwriter`: write text, numbers, formulas and hyperlinks to multiple worksheets in an Excel 2007+ XLSX file.
- `xlrd`: Extracts data from Excel files (.xls and .xlsx, versions 2.0 onwards).
- `xlwt`: Writes and formats Excel files compatible with Microsoft Excel versions 95 to 2003.
- `xlutils`: Contains a set of advanced tools for manipulating Excel files (requires `xlrd` and `xlwt`).

You would need to install each separately if you want to use them; however, in this tutorial we will use Pandas `ExcelFile` class that requires `xlrd` to demonstrate how to parse Excel files.

```python
import pandas as pd

excel_data = pd.ExcelFile('SOWC 2014 Stat Tables_Table 2.xlsx')
```



```python
# show the names of all the sheets
excel_data.sheet_names
```

![截屏2021-01-28 上午7.15.14](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230021693.png)

```python
# excel_data is an excel object
# after use the parse function, the result is a dataframe

df = excel_data.parse('Table 2 ')
df.head(100)
```

![截屏2021-01-28 上午7.17.04](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230021700.png)

```python
df.shape
```

![截屏2021-01-28 上午7.18.09](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230021196.png)

```python
df.drop(['Unnamed: 2','Unnamed: 3'], 1)
df.shape
```

![截屏2021-01-28 上午7.19.19](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230021425.png)

```python
df = df.dropna(0, how = 'all')
df = df.dropna(1, how = 'all')
```



### To set index

```python
# To reset index
df.reset_index(inplace = True)
df.drop(['index'], axis = 1, inplace = True)

# Another way
df.index = range(len(df.index))
```

```python
# Set the country names as row indices
df.set_index(df['TABLE 2. NUTRITION'].values, inplace = True)
df.drop(['TABLE 2. NUTRITION'], axis = 1, inplace = True)

# Reindex column names
df.columns = list(range(len(df.columns))) 
```

![截屏2021-01-28 上午7.40.18](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230021814.png)



### Tidy up all columns

```python
# A FOR loop over odd-numbered columns
for col_idx in range(1, 24, 2): 
    # A For loop over all the rows
    for row_idx in range(len(df)):
        # A IF statement to check
        #    1. If the cell value in the odd-numbered column is not NaN （means x）, then merge it with the cell value in 
        #       the even-numbered column.
        #    2. Otherwise, do nothing.
        if not pd.isnull(df.iloc[row_idx, col_idx]):
            df[col_idx-1][row_idx] = str(df[col_idx-1][row_idx]) + ' ' + str(df[col_idx ][row_idx])  
df.head()
```

![截屏2021-01-28 上午7.44.00](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230021054.png)

```python
# What will range(1,24,2) give to us?

# Return an object that produces a sequence of integers from start (inclusive)
# to stop (exclusive) by step. 

for col_idx in range(1, 24, 2): 
     df.drop(col_idx, 1, inplace = True)
df.head()
```

![截屏2021-01-28 上午7.44.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture/202111230021049.png)

```python
df.columns = ["Low birthweight (%)", \
              "Early initiation of breastfeeding (%)", \
              "Exclusive breastfeeding <6 months (%)", \
              "Introduction of solid, semi-solid or soft foods 6–8 months (%)", \
              "Breastfeeding at age 2 (%)", \
              "Underweight (%) moderate and severe", \
              "Underweight (%) severe", \
              "Stunting (%) moderate and severe", \
              "Wasting (%) moderate and severe",\
              "Overweight (%) moderate and severe", \
              "Vitamin A supplementation, full coverage(%)", \
              "Iodized salt consumption (%)" ]
df.head()
```

![截屏2021-01-28 上午7.45.05](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-28 上午7.45.05.png)

```python
df.to_csv('./en_final_table_2_1.csv', sep='\t', encoding='utf-32')
```

