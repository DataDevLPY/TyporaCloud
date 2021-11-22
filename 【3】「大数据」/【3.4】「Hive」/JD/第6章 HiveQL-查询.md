## 6.1 select from 语句

**6.1.2 使用列值进行计算**

```
select upper(name), salary, deductions["Federal Taxes"], 
	round(salary*(1-deductions["Federal Taxes"])) from employees;
```



**6.1.4 使用聚合函数**

```
-- 提高聚合性能
set hive.map.aggr=true;
```



**6.1.9 Hive避免进行MapReduce**

```
set hive.exec.mode.local.auto=true;
```



## 6.2 Where 语句

**6.2.3 Like and Rlike**

```
select name, address.street
from employees 
where address.street rlike '.*(Chicago|Ontario).*';


select name, address.street
from employees 
where address.street like '%Chicago%' or address.street like 'Ontario';
```





## 6.3 Group by





## 6.6 sort by 的 distribute by





## 6.7 cluster by



## 6.9 抽样查询

```
select * from numbers tablesample(bucket 3 out of 10 on rand()) s;
```



**6.9.2 分桶表的输入剪裁**

```
create table numbers_bucketed(
number int
)
clustered by (number) into 3 buckets;


set hive.enforce.bucketing=true;

insert overwrite table numbers_bucketed 
select number from numbers;

dfs -ls /user/hive/warehouse/mydb.db/numners_bucketed;
```



