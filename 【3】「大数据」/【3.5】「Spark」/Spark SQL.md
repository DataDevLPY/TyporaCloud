# Spark SQL

## Creating the view in SQL

```python
file_df.createOrReplaceTempView('file_sql')

file_df.createOrReplaceGlobalTempView('file_sql')
```



## Selecting

```python
sql_1 = spark.sql('''
		SELECT
		FROM file_sql
''')

sql_1.collect()
```

