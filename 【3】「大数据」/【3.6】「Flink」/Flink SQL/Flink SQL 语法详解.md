## Flink SQL Job的组成

Apache Flink SQL编写的计算Job由读取原始数据，计算逻辑和写⼊计算结果数据三部分组成。

![image-20210506142347038](C:\Users\lipeiyang10\AppData\Roaming\Typora\typora-user-images\image-20210506142347038.png)

* Source Operator - Soruce operator是对外部数据源的抽象, ⽬前Apache Flink内置了很多常
  ⽤的数据源实现，⽐如上图提到的Kafka。
* Query Operators - 查询算⼦主要完成如图的Query Logic，⽬前⽀持了Union，Join，
  Projection,Difference, Intersection以及window等⼤多数传统数据库⽀持的操作。
* Sink Operator - Sink operator 是对外结果表的抽象，⽬前Apache Flink也内置了很多常⽤的
  结果表的抽象，⽐如上图提到的Kafka。



## Flink SQL核⼼算⼦

⽬前Flink SQL⽀持Union，Join，Projection，Difference，Intersection以及Window等⼤多数传
统数据库⽀持的操作。



#### SELECT

SELECT ⽤于从数据集/流中选择数据，语法遵循ANSI-SQL标准，语义是关系代数中的投影
(Projection),对关系进⾏垂直分割，消去某些列。



#### WHERE

WHERE 是对满⾜⼀定条件的数据进⾏过滤， WHERE ⽀持=, <, >, <>, >=, <=以及AND ， OR 等表达式的组合，最终满⾜过滤条件的数据会被选择出来。并且 WHERE 可以结合IN , NOT IN 联合使⽤，

PS：IN是关系代数中的Intersection， NOT IN是关系代数的Difference。



#### GROUP BY

GROUP BY 是对数据进⾏分组的操作。



#### UNION ALL

UNION ALL 将两个表合并起来，要求两个表的字段完全⼀致，包括字段类型、字段顺序,语义对应
关系代数的Union，只是关系代数是Set集合操作，会有去重复操作，UNION ALL 不进⾏去重。



#### UNION

UNION 将两个流给合并起来，要求两个流的字段完全⼀致，包括字段类型、字段顺序，并其
UNION 不同于UNION ALL，UNION会对结果数据去重，与关系代数的Union语义⼀致。



#### JOIN

JOIN ⽤于把来⾃两个表的⾏联合起来形成⼀个宽表，Apache Flink⽀持的JOIN类型：

* JOIN - INNER JOIN
* LEFT JOIN - LEFT OUTER JOIN
* RIGHT JOIN - RIGHT OUTER JOIN
* FULL JOIN - FULL OUTER JOIN

FULL JOIN 相当于 RIGHT JOIN 和 LEFT JOIN 之后进⾏UNION ALL 操作。PS：Flink不⽀持
Cross JOIN。



#### Window

在Apache Flink中有2种类型的Window，⼀种是OverWindow，即传统数据库的标准开窗，每⼀个
元素都对应⼀个窗⼝。⼀种是GroupWindow，⽬前在SQL中GroupWindow都是基于时间进⾏窗⼝划分的。

  * **Over Window**

    ​	Apache Flink中对OVER Window的定义遵循标准SQL的定义语法。按ROWS和RANGE分类是传	统数据库的标准分类⽅法，在Apache Flink中还可以根据时间类型(ProcTime/EventTime)和窗	⼝的有限和⽆限(Bounded/UnBounded)进⾏分类，共计8种类型。为了避免⼤家对过细分类造	成困扰，我们按照确定当前⾏的不同⽅式将OVER Window分成两⼤类进⾏介绍，如下:

    			* ROWS OVER Window - 每⼀⾏元素都视为新的计算⾏，即，每⼀⾏都是⼀个新的窗		
       * RANGE OVER Window - 具有相同时间值的所有元素⾏视为同⼀计算⾏，即，具有相同时间值的所有⾏都是同⼀个窗⼝。

    

    *Bounded ROWS OVER Window*
    Bounded ROWS OVER Window 每⼀⾏元素都视为新的计算⾏，即，每⼀⾏都是⼀个新的窗⼝。

```sql
SELECT
agg1(col1) OVER(
[PARTITION BY (value_expression1,..., value_expressionN)]
ORDER BY timeCol
ROWS
BETWEEN (UNBOUNDED | rowCount) PRECEDING AND CURRENT ROW) AS colName,
...
FROM Tab1
```

​							** value_expression - 进⾏分区的字表达式；

​							** timeCol - ⽤于元素排序的时间字段；

​							** rowCount - 是定义根据当前⾏开始向前追溯⼏⾏元素。

Example:

```sql
SELECT
itemID,
itemType,
onSellTime,
price,
MAX(price) OVER (
PARTITION BY itemType
ORDER BY onSellTime
ROWS BETWEEN 2 preceding AND CURRENT ROW) AS maxPrice -- 当前⾏向前追溯两
⾏
FROM item_tab
```



​		*Bounded RANGE OVER Window*
​		Bounded RANGE OVER Window 具有相同时间值的所有元素⾏视为同⼀计算⾏，即，具有相同		时间值的所有⾏都是同⼀个窗⼝。

```sql
SELECT
agg1(col1) OVER(
[PARTITION BY (value_expression1,..., value_expressionN)]
ORDER BY timeCol
RANGE
BETWEEN (UNBOUNDED | timeInterval) PRECEDING AND CURRENT ROW) AS colName,
...
FROM Tab1
```

​					** value_expression - 进⾏分区的字表达式；

​					** timeCol - ⽤于元素排序的时间字段；

​					** timeInterval - 是定义根据当前⾏开始向前追溯指定时间的元素⾏；

Example:

```sql
SELECT
itemID,
itemType,
onSellTime,
price,
MAX(price) OVER (
PARTITION BY itemType
ORDER BY rowtime
RANGE BETWEEN INTERVAL '2' MINUTE preceding AND CURRENT ROW) AS
maxPrice -- 两分钟前的数据
FROM item_tab
```



* **Group Window**

  ​	根据窗⼝数据划分的不同，⽬前Apache Flink有如下3种Bounded Winodw:
    * Tumble - 滚动窗⼝，窗⼝数据有固定的⼤⼩，窗⼝数据⽆叠加；

    * Hop - 滑动窗⼝，窗⼝数据有固定⼤⼩，并且有固定的窗⼝重建频率，窗⼝数据有叠加；

    * Session - 会话窗⼝，窗⼝数据没有固定的⼤⼩，根据窗⼝数据活跃程度划分窗⼝，窗⼝数据⽆叠加。

      

说明： Aapche Flink 还⽀持UnBounded的 Group Window，也就是全局Window，流上所有数据都在⼀个窗⼝⾥⾯，语义⾮常简单，这⾥不做详细介绍了。



​				*Tumble*

```sql
SELECT
[gk],
[TUMBLE_START(timeCol, size)],
[TUMBLE_END(timeCol, size)],
agg1(col1),
...
aggn(colN)
FROM Tab1
GROUP BY [gk], TUMBLE(timeCol, size)
```



​					** [gk] - 决定了流是Keyed还是/Non-Keyed;

​					** TUMBLE_START - 窗⼝开始时间;

​					** TUMBLE_END - 窗⼝结束时间;

​					** timeCol - 是流表中表示时间字段；

​					** size - 表示窗⼝的⼤⼩，如 秒，分钟，⼩时，天。

Example:

```sql
SELECT
region,
TUMBLE_START(rowtime, INTERVAL '2' MINUTE) AS winStart,
TUMBLE_END(rowtime, INTERVAL '2' MINUTE) AS winEnd,
COUNT(region) AS pv
FROM pageAccess_tab
GROUP BY region, TUMBLE(rowtime, INTERVAL '2' MINUTE)
```





​				*Hop*

Hop 滑动窗⼝和滚动窗⼝类似，窗⼝有固定的size，与滚动窗⼝不同的是滑动窗⼝可以通过slide参
数控制滑动窗⼝的新建频率。因此当slide值⼩于窗⼝size的值的时候多个滑动窗⼝会重叠。

```sql
SELECT
[gk],
[HOP_START(timeCol, slide, size)] ,
[HOP_END(timeCol, slide, size)],
agg1(col1),
...
aggN(colN)
FROM Tab1
GROUP BY [gk], HOP(timeCol, slide, size)
```

​					** [gk] 决定了流是Keyed还是/Non-Keyed;
​					** HOP_START - 窗⼝开始时间;
​					** HOP_END - 窗⼝结束时间;
​					** timeCol - 是流表中表示时间字段；
​					** slide - 是滑动步伐的⼤⼩；
​					** size - 是窗⼝的⼤⼩，如 秒，分钟，⼩时，天；

```sql
SELECT
HOP_START(rowtime, INTERVAL '5' MINUTE, INTERVAL '10' MINUTE) AS winStart,
HOP_END(rowtime, INTERVAL '5' MINUTE, INTERVAL '10' MINUTE) AS winEnd,
SUM(accessCount) AS accessCount
FROM pageAccessCount_tab
GROUP BY HOP(rowtime, INTERVAL '5' MINUTE, INTERVAL '10' MINUTE)
```



​				*Session*

Seeeion 会话窗⼝ 是没有固定⼤⼩的窗⼝，通过session的活跃度分组元素。不同于滚动窗⼝和滑动窗⼝，会话窗⼝不重叠,也没有固定的起⽌时间。⼀个会话窗⼝在⼀段时间内没有接收到元素时，即当出现⾮活跃间隙时关闭。⼀个会话窗⼝ 分配器通过配置session gap来指定⾮活跃周期的时⻓.



```sql
SELECT
[gk],
SESSION_START(timeCol, gap) AS winStart,
SESSION_END(timeCol, gap) AS winEnd,
agg1(col1),
...
aggn(colN)
FROM Tab1
GROUP BY [gk], SESSION(timeCol, gap)
```

​				** [gk] 决定了流是Keyed还是/Non-Keyed;
​				** SESSION_START - 窗⼝开始时间；
​				** SESSION_END - 窗⼝结束时间；
​				** timeCol - 是流表中表示时间字段；
​				** gap - 是窗⼝数据⾮活跃周期的时⻓；

```sql
SELECT
region,
SESSION_START(rowtime, INTERVAL '3' MINUTE) AS winStart,
SESSION_END(rowtime, INTERVAL '3' MINUTE) AS winEnd,
COUNT(region) AS pv
FROM pageAccessSession_tab
GROUP BY region, SESSION(rowtime, INTERVAL '3' MINUTE)
```

