## 5.1 向管理表中装载数据

```
load data local inpath '${env:HOME} /california-employees'
overwrite into table employees
partition (country:'US', state='CA');
```



## 5.2 通过查询语句向表中插入数据

```
insert overwrite table employees
partition (country = 'US', state = 'OR')
select * from staged_employees se
where se.cnty = 'US' and se.st='OR';
```



**动态分区插入**

静态分区必须出现在动态分区之前。

![Screen Shot 2021-04-01 at 8.42.12 PM](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220100921.png?token=AWS37JKXKSVJD4GQGBNGBJ3BTJ5U6)

```
insert overwrite table employees
partition (country, state)
select ..., se.cnty, se.st
from staged_employees se;

insert overwrite table employees
partition (country, state)
select ..., se.cnty, se.st
from staged_employees se
where se.cnty='US';
```



## 5.4 导出数据

``` 
## copy
hadoop fs -cp source_path target_path

insert overwrite local directory '/tmp/ca_employees'
select name, salary, address
from employees
where se.state = 'CA';


如果没有分隔符
from staged_employees se
insert overwrite local directory '/tmp/ca_employees'
select * where se.cty = 'US' and se.st = 'OR'
insert overwrite local directory '/tmp/ca_employees'
select * where se.cty = 'US' and se.st = 'CA'
insert overwrite local directory '/tmp/ca_employees'
select * where se.cty = 'US' and se.st = 'IL';
```

