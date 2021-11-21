## 4.1 Hive中的数据库

```
create database financials;

create database if not exists financials;
```

查看数据库

```
show databases;

// Using Like

show databases like 'h.*';
```

修改数据库位置

```
在创建的时候
create databases financials
location '/my/preferred/directory';
comment 'Holds all financial tables';
with dbproperties ('creator'='Mark Moneybags', 'date'='2012-001-02');

查看
describe database financials;
describe database extend financials
```

USE命令

```
将某个数据库设置为用户当前的工作数据库
use financials;
```

删除数据库

```
直接删除所有表再删除这个数据库
drop database if exists financials cascade;
```



## 4.2修改数据库

```
数据库的其他元数据信息是不可更改的，包括数据库名和数据库所在的目录位置。
alter database financials set dbproperties('edited-by'='Joe Dba')
```



## 4.3 创建表

具有更广泛的灵活性，可以定义表的数据文件存储在什么位置、使用什么样的存储格式

```
create table if not exists mydb.employees (
	name					string					comment 'Employee name',
	salary				float						comment 'Employee salary',
	subordinates	array<string>		comment 'Names of subordinates',
	deductions		map<string, float>
								comment 'Keys are deductions names, values are percentages',
	address				struct<street:string, city:string, state:string, zip:int>
								comment 'Home address'
)

comment 'Description of the table'
tblproperties('creator'='me', 'created_at'='2012-01-02 10:00:00', ...)
location '/user/hive/warehouse/mydb.db/employees';
```

查看table属性信息

```
show tblproperties table_name
```

复制表

```
create table if not exists mydb.employees2 like mydb.employees;
```

列举表

```
use mydb;
show tables;
show tables 'empl.*'

use default;
show tables in mydb;
```

查看详细表结构信息

```
describe extended mydb.employees;
describe formatted mydb.empoyees;
```



**4.3.2 外部表**

```
create external table if not exists stocks(
	exchange		string,
	symbol			string,
	ymd					string,
	price_open	float,
	price_high	float,
	price_low		float,
	price_close	float,
	volume			int,
	price_adj_close		float
)
row format delimited fields terminated by ','
location '/data/stocks';



create external table if not exists mydb.employees3
like mydb.employees
location '/path/to/data';
```

````
"11:22 AM" "0:22 PM"
````



## 4.4 分区表、管理表

```
create table if not exists mydb.employees (
	name					string					comment 'Employee name',
	salary				float						comment 'Employee salary',
	subordinates	array<string>		comment 'Names of subordinates',
	deductions		map<string, float>
								comment 'Keys are deductions names, values are percentages',
	address				struct<street:string, city:string, state:string, zip:int>
								comment 'Home address'
)
partitioned by (country string, state string);
```

查询

```
select * from employees
	where country = 'US'
	and state = 'IL';
	

set hive.mapred.mode = nonstrict;
select e.name, e.salary from employee e limit 100;

可以通过 show partitions查看表中存在的所有分区：
show partitions employees;
show partitions employees partition(country='US');
show partitions employees partition(country='US', state='AK');

describe extended employees 也会显示分区键
```



**4.4.1 外部分区表**

```
create external table if not exists log_messages(
	hms					int,
	severity		string,
	server			string,
	process_id	int,
	message			string
)
partitioned by (year int, month int, day int)
row format delimited fields terminated by '\t';

alter table log_messages add partition(year=2012, month=1, day=2)
location 'hdfs://master_server/data/log_messages/2012/01/02';
```

将分区下的数据拷贝到s3中，可以使用hadoop distcp：

```
hadoop distcp/data/log_message/2011/12/02 s3n: //ourbucket/logs/2011/12/02
```

修改表，将分区路径指向到S3路径：

```
alter table log_messages partition(year=2012, month=1, day=2)
set location 's3n://ourbucket/logs/2011/01/02';
```

使用hadoop fs -rmr删除掉HDFS中的这个分区数据

```
hadoop fs -rmr /data/log_messages/2011/01/02
```



**4.4.2 自定义表的存储格式**

```
create table employees
(
	name					string,
	salary				float,
	subordinates	array<string>,
	deductions		map<string, float>,
	address				struct<street:string, city:string, state:string, zip:int>
)
row format delimited
fields terminated by '\001'							-- 列分割 ^A
collection items terminated by '\002'		-- 集合元素间分割 ^B
map keys terminated by '\003'						-- map之间的键值分割 ^C
lines terminated by '\n'
stored as textfile;
```



```
create external table if not exists stocks(
	exchange			string,
	symbol				string,
	ymd						string,
	price_open		float,
	price_high		float,
	price_low			float,
	price_close		float,
	volume				int,
	price_adj_close		float
)
clustered by (exchange, symbol)
stored by (ymd ASC)
into 96 buckets
row format delimited fields terminated by ','
location '/data/stocks';
```



## 4.5 删除表

```
drop table if exists employees;
```



## 4.6 修改表

**4.6.1 表重命名**

```
alter table log_messages rename to logmsgs;
```

**4.6.2 增加、修改和删除表分区**

```
alter table log_messages add if not exists
partition (year=2011,month=1,day=1) location 'logs/2011/01/01'
partition (year=2011,month=1,day=2) location 'logs/2011/01/02'
partition (year=2011,month=1,day=3) location 'logs/2011/01/03'
```



```
alter table log_messages drop if exists partition(year=2011,month=12,day=2);
```

**4.6.3 修改列信息**

```
alter table log_messages
change column hms hours_minutes_seconds int
comment 'The hours, minutes, and seconds part of the timestamp'
after severity;
```

**4.6.4增加列**

```
alter table log_messages add columns(
	app_name		string,
	session_id	long
);
```



**4.6.5 删除或者替换列**

移除了之前所有的字段并重新制定了新的字段

```
alter table log_messages replace columns(
	hours_mins_secs		int,
	severity					string,
	message						string
);
```



**4.6.6 修改表属性**

```
可以修改但是无法删除
alter table log_messages set tblproperties(
	'notes' = ‘’
);
```

