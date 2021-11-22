删除列

```
alter table <table_name> drop column <col_name>;
```



删除指定对象

```
delete from <表名>
	where <条件>;
```



只能舍弃表中的所有

```
truncate <表名>;
whether
```



创建视图

```
cretae view ProductJim (type, name) as
	select type, name
  from table1;

-- cascade删除关联视图
drop view ProductJim cascade;
```



## 函数

MySQL

```
length()

len()

lower()
upper()

replace(str1,str2,str3)
-- str1是对象字符串
-- 替换前的字符串
-- 替换后的字符串

substring(str1 from 3 for 2)
-- substr(对象字符串，截取的起始位置，截取的字符数)   Oracle

```



日期函数

```
current_time

extract (日期元素 from 日期)
```



cast函数

```
cast(转换前的值 as 想要转换的数据类型)
```



将NULL转换成其他值

```
SELECT COALESCE(NULL, 1) AS col_1,
COALESCE(NULL, 'test', NULL) AS col_2,
COALESCE(NULL, NULL, '2009-11-01') AS col_3
FROM DUAL;
```



## 表的加减法

```
union
union all
intersect
except
```



## 窗口函数

```
<窗口函数> OVER ([PARTITION BY <列清单>] ORDER BY <排序用列清单>)
```

```
SELECT product_name, product_type, sale_price,
RANK () OVER (PARTITION BY product_type
ORDER BY sale_price) AS ranking
FROM Product;
```

●RANK函数

计算排序时，如果存在相同位次的记录，则会跳过之后的位次。

例）有3 条记录排在第1 位时：1 位、1 位、1 位、4 位……

●DENSE_RANK函数

同样是计算排序，即使存在相同位次的记录，也不会跳过之后的位次。

例）有3 条记录排在第1 位时：1 位、1 位、1 位、2 位……

●ROW_NUMBER函数

赋予唯一的连续位次。

例）有3 条记录排在第1 位时：1 位、2 位、3 位、4 位……

**作为窗口函数使用的聚合函数**

```
SELECT product_id, product_name, sale_price,
SUM (sale_price) OVER (ORDER BY product_id) AS current_sum
FROM Product;


SELECT product_id, product_name, sale_price,
AVG (sale_price) OVER (ORDER BY product_id) AS current_avg
FROM Product;
```



**移动平均**

```
SELECT product_id, product_name, sale_price,
AVG (sale_price) OVER (ORDER BY product_id
ROWS 2 PRECEDING) AS moving_avg
FROM Product;
```



## GROUPING运算符

**ROLLUP——同时得出合计和小计**

```
SELECT product_type, regist_date, SUM(sale_price) AS sum_price
FROM Product
GROUP BY product_type, regist_date;


SELECT GROUPING(product_type) AS product_type,
GROUPING(regist_date) AS regist_date, SUM(sale_price) AS sum_price
FROM Product
GROUP BY ROLLUP(product_type, regist_date);
```

