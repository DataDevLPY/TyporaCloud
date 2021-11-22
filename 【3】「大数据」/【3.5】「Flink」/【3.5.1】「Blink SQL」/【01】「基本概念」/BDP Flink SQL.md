## 数据源/⽬标源创建

1. 点击左侧菜单的数据源管理进⼊数据源界⾯；

2. 点击注册数据源进⼊数据源注册界⾯

   ----

   1. 选择数据源所属应⽤；

   2. 数据源类型⽬前只⽀持MessageQueue中间件，根据数据来源选择JDQ or JMQ

   3. 选择Topic，数据源名称会⾃动⽣成

   4. 数据类型的选择与配置，相⻅BDP平台帮助⽂档：http://bdp.jd.com/helpCenter/front/showDocumentList.html?docId=458

   5. 点击注册完成数据源注册

      ----

      1. 选择左侧菜单栏数据源管理；

      2. 点击注册⽬标源，跳转进⼊⽬标源(sink)注册界⾯

         -----

         1. 选择所属应⽤
         2. 选择⽬标源类型，flink SQL将Stream流先抓换为动态表，进⾏计算，计算完毕，将动态表写⼊外部系统时，需要转换为不同类型的Stream流(⽬前flink Table &。SQL⽀持Append-only流、Retract 流、Upsert 流)，根据⽬标源类型特性(例如：是否⽀持update)与业务场景选择
         合适的⽬标源；
         3. 根据选择的⽬标源，配置连接参数
         4. 点击注册



## Flink SQL任务基本组成与详解

⼀个完成的Flink SQL Job由source，transform，sink组成；

与flink Java任务⼀致
source，sink 以及各个参数含义
transform语法

----

1. 选择JDQ 消费者的clientId；
2. 选择脏数据处理策略：忽略/抛出异常；如果业务场景容忍部分脏数据的丢失，可以选择忽略；
3. 时间语义根据业务场景需求选择：processing time/event_time
4. flink SQL event_time时间列的名称
5. Event_time字段列别名，可以在flink sql中使⽤别名做查询
6. 选择watermark⽣成⽅式
7. 点击完成后即可在SQL界⾯⽣成下⾯的 CREATE SOURCE TABLE 语句
PS：数据拉取( startmode )模式需要修改为： groupoffsets ，这样回放数据才会⽣效。这个
需要在source⽣成之后直接在SQL中修改即可。

```sql
CREATE SOURCE TABLE source_1(
mid long,
db string,
sch string,
tab string,
opt string,
ts long,
ddl string,
err string,
src map,
cur map,
cus map
)
with(
password='xxxxxx', # JDQ accesskey
clientid='xxxxxx', # JDQ consumer clientid
sourceType='jdq',
startmode='latest', # 任务启动时从JDQ数据拉取⽅式
watermarktype='watermarksPeriodicAscending', # watermark⽣成⽅式
timecolumnalias='rowtime', # event_time模式下，时间列的别名
processmethod='0', # 对于脏数据处理模式
topic='xxxxxx', # source来源topic名称
timetype='eventtime', # 时间语义选择event_time
timecolumn='cur['ts']', # 时间语义对应的字段
username='xxxxxxxx' # JDQ 应⽤域名
);
```



## Sink

BDP⽬前⽀持的sink源有：JMQ，JDQ，JimDB(Redis)，HBase，ClickHouse
⽬前项⽬中仅使⽤过JimDB和JDQ作为⽬标源。后续会探索ClickHouse和Elasticsearch。

#### Sink源创建

选择以T开头的sink数据源，以jimDB源为例创建Sink源。

​		存储类型：对应redis的数据结构和redis的命令。

​		SINK MODE：append，retract，upsert，它们的区别和说明如下，根据业务场景和数据使用要		求选择适合的sink mode。

			* Append-only 流： 仅通过 INSERT 操作修改的动态表可以通过输出插⼊的⾏转换为流。
   * Retract 流： retract 流包含两种类型的 message： add messages 和 retract
     messages 。通过将INSERT 操作编码为 add message、将 DELETE 操作编码为
     retract message、将 UPDATE 操作编码为更新(先前)⾏的 retract message 和更新
     (新)⾏的 add message，将动态表转换为 retract 流。
   * Upsert流：upsert 流包含两种类型的 message： upsert messages 和delete
     messages。转换为 upsert 流的动态表需要(可能是组合的)唯⼀键。通过将 INSERT
     和 UPDATE 操作编码为 upsert message，将 DELETE 操作编码为 delete message
     ，将具有唯⼀键的动态表转换为流。消费流的算⼦需要知道唯⼀键的属性，以便正确
     地应⽤ message。与 retract 流的主要区别在于 UPDATE 操作是⽤单个 message 编
     码的，因此效率更⾼。



```sql
CREATE SINK TABLE sink_1(
key string,
mapkey string,
mapvalue string
)
with(
storeType='hset', # redis数据结构与命令
sinkmode='retract', # 动态表转stream策略
sinkType='jimdb', # ⽬标源类型
jimurl='xxxxxxxx', # jimdb url
ttl='1', # redis key 过期时间设置
ttlunit='DAYS'
);
```



#### Flink SQL 数据计算

Flink SQL⽀持标准SQL语法，并使⽤ANSI SQL标准的Apache Calcite解析、优化SQL。此外flink SQL最终被转换为DataStream/DataSet，⽤于实现批流⼀体处理。

```sql
SELECT [ ALL | DISTINCT ]
{ * | projectItem [, projectItem ]* }
FROM tableExpression
[ WHERE booleanExpression ]
[ GROUP BY { groupItem [, groupItem ]* } ]
[ HAVING booleanExpression ]
[ WINDOW windowName AS windowSpec [, windowName AS windowSpec ]* ]
```



与标准SQL不同的是，Flink SQL⽀持window(窗⼝)函数，⽤于实时加⼯：

* 滚动窗⼝（TUMBLE）
* 滑动窗⼝（HOP）
* 会话窗⼝（SESSION）
* OVER窗⼝（OVER Window）



```sql
select
case
when company_code = '1' THEN '1'
else '9' end as entrust_owner_type,
wh_no,
cur_status,
src_status,
order_no,
date_str,
rowtime
from(select
opt,
if(cur['order_no'] IS NOT NULL,cur['order_no'],src['order_no']) as
order_no,
if(cur['company_code'] IS NOT NULL,cur['company_code'],src['company_code'])
as company_code,
if(cur['wh_no'] IS NOT NULL,cur['wh_no'],src['wh_no']) as wh_no,
if(cur['yn'] IS NOT NULL,cur['yn'],src['yn']) as yn,
if(cur['create_time'] IS NOT NULL,cur['create_time'], '') as create_time,
if(cur['status'] IS NOT NULL,cur['status'], '') as cur_status,
if(src['status'] IS NOT NULL,src['status'], '') as src_status,
rowtime,
cast(DATE_FORMAT(cast(TIMESTAMPADD(HOUR,8,rowtime) as varchar), 'yyyyMMdd')
as varchar) as date_str
from S_wl_rtdw_ods_wms_inv_rep_waves_bak) where yn = '1' and create_time <> ''
and REPLACE(SUBSTRING(create_time, 0, 10),'-','') = date_str;
```



## 完成的Flink SQL任务

source，transform，sink组成了⼀个完整的Flink Job。

```sql
CREATE SOURCE TABLE source_1(
mid long,
db string,
sch string,
tab string,
opt string,
ts long,
ddl string,
err string,
src map,
cur map,
cus map
)
with(
password='xxxxxx', # JDQ accesskey
clientid='xxxxxx', # JDQ consumer clientid
sourceType='jdq',
startmode='latest', # 任务启动时从JDQ数据拉取⽅式
watermarktype='watermarksPeriodicAscending', # watermark⽣成⽅式
timecolumnalias='rowtime', # event_time模式下，时间列的别名
processmethod='0', # 对于脏数据处理模式
topic='xxxxxx', # source来源topic名称
timetype='eventtime', # 时间语义选择event_time
timecolumn='cur['ts']', # 时间语义对应的字段
username='xxxxxxxx' # JDQ 应⽤域名
);
```



```sql
CREATE SINK TABLE sink_1(
key string,
mapkey string,
mapvalue string
)
with(
storeType='hset', # redis数据结构与命令
sinkmode='retract', # 动态表转stream策略
sinkType='jimdb', # ⽬标源类型
jimurl='xxxxxxxx', # jimdb url
ttl='1', # redis key 过期时间设置
ttlunit='DAYS'
);


CREATE TABLE transfrom_1
select
case
when company_code = '1' THEN '1'
else '9' end as entrust_owner_type,
wh_no,
cur_status,
src_status,
order_no,
date_str,
rowtime
from(select
opt,
if(cur['order_no'] IS NOT NULL,cur['order_no'],src['order_no']) as
order_no,
if(cur['company_code'] IS NOT NULL,cur['company_code'],src['company_code'])
as company_code,
if(cur['wh_no'] IS NOT NULL,cur['wh_no'],src['wh_no']) as wh_no,
if(cur['yn'] IS NOT NULL,cur['yn'],src['yn']) as yn,
if(cur['create_time'] IS NOT NULL,cur['create_time'], '') as create_time,
if(cur['status'] IS NOT NULL,cur['status'], '') as cur_status,
if(src['status'] IS NOT NULL,src['status'], '') as src_status,
rowtime,
cast(DATE_FORMAT(cast(TIMESTAMPADD(HOUR,8,rowtime) as varchar), 'yyyyMMdd')
as varchar) as date_str
from source_1) where yn = '1' and create_time <> '' and
REPLACE(SUBSTRING(create_time, 0, 10),'-','') = date_str;
INSERT INTO sink_1
select
'test',
CONCAT_WS('_',company_type,store_id),
CAST(out_store_daily_num as varchar)
from transfrom_1;
```





## BDP Flink SQL任务发布

任务发布流程，启动参数，任务配置，集群配置，job拓扑图修改



1. 点击SQL界⾯右侧的运⾏配置，进⼊flink job运⾏时的配置：
  * 集群：上⾯创建好的集群，可以在同⼀个集群上部署多个flink SQL Job；
  * 默认并⾏度：由于SQL中⽆法配置各个算⼦的并⾏度，因此需 要设置默认并⾏度；在
    提交任务，形成StreamGraph后可以修改各个算⼦并⾏度
  * checkpoint MODE：EXACTLY_ONCE，AT_LEAST_ONCE
  * checkponit间隔(ms)：两次checkpoint之间的时间间隔
  * 注意：需要勾选任务异常告警和使⽤Blink Planner；Blink Planner是阿⾥内部开发
    SQL优化器，flink planner 是Flink⽼版优化器，1.9版本之后阿⾥贡献给flink开源社
    区，1.11成为默认的SQL优化器
  * 任务配置：任务的⼀些⾼级配置，使⽤<K, V>的数据结构

2. 点击校验，检查SQL语法，通过校验即可点击下⼀步
3. 点击下⼀步提交任务，构建拓扑图，拓扑图如下所示，可以在拓扑上调整各个算⼦的并⾏度
4. 点击上线审批，即可完成⼀次发布上线



