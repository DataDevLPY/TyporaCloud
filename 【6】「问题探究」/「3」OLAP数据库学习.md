# OLAP数据库学习



## 一、ClickHouse

### 1. 核心特性

- 1）完备的DBMS功能：具备DDL、DML、数据库/表权限控制及分布式管理等功能。
- 2）列式存储和数据压缩：数据压缩比高（支持LZ4, ZSTD压缩算法)，极大的节约了IO带宽。
  - 按列存储，减少了**数据扫描范围**
  - 压缩的本质是按照一定步长对数据进行匹配扫描，当发现重复部分的时候就进行编码转换，减少了**数据传输时的大小**
  - clickhouse存储的文件不管是索引，还是bin文件都是有序的。不然没法快速过滤数据
  - count上有优势
- 3）向量化执行引擎：多核并行计算、向量化执行与SIMD。
  - 通过数据并行以提高性能，在CPU寄存器层面实现数据的并行操作，消除程序中循环的操作
- 4）SQL支持：支持标准的SQL语法，内置功能齐全的分析统计函数等。支持多索引：主键索引、稀疏索引等。
  - 支持繁多库函数（例如IP转化，URL分析等，预估计算/HyperLoglog等）；
  - 支持数组(Array)和嵌套数据结构(Nested Data Structure)；
- 5）多样化的表引擎：拥有合并树、内存、文件、接口和其他6大类20多种表引擎。
- 6）多线程与分布式：
  - 向量化执行是通过数据级并行的方式提升了性能，而多线程处理则是通过线程级并行的方式实现了性能的提升
  - ClickHouse在数据存取方面，既支持分区 ( 纵向扩展，利用多线程原理 )，也支持分片 ( 横向扩展，利用分布式原理 )
- 7）多主架构：ClickHouse采用Multi-Master多主架构，集群中的每个节点角色对等，客户端访问任意一个节点都能得到相同的效果
- 8）数据分片与分布式查询：
  - 数据分片是将数据进行横向切分，这是一种在面对海量数据的场景下，解决存储和查询瓶颈的有效手段，是一种分治思想的体现。
  - ClickHouse提供了本地表（Local Table）与分布式表（Distributed Table）的概念。一张本地表等同于一份数据的分片。而分布式表本身不存储任何数据，它是本地表的访问代理，其作用类似分库中间件。借助分布式表，能够代理访问多个数据分片，从而实现分布式查询。

### 2. 问题

* 1）Clickhouse并不适合许多小数据高频插入，批量写入日志会有一定延迟，无法及时对新导入的数据进行合并，从而影响查询性能。
* 2）不支持事务，且缺少update和delete
* 3）数据迁移：
  * 大批量采用ClickHouse_copier或者复制数据的方式实现
  * 小批量可采用remote语句进行迁移
* 4）虽然支持Join，但是join的性能不好
* 5）MergeTree合并不完全
* 6）难以实现高并发查询，且无法通过扩容提高并发能力
  * Clickhouse快是因为采用了并行处理机制，即使一个查询，也会用服务器一半的CPU去执行，所以ClickHouse不能支持高并发的使用场景，默认单查询使用CPU核数为服务器核数的一半，安装时会自动识别服务器核数，可以通过配置文件修改该参数




## 二、ElasticSearch

```sql
'''
Elasticsearch是一个开源的高扩展的分布式全文检索引擎

NoSQL中文档数据库的代表
'''
```

![截屏2021-12-12 下午8.54.41](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/202112122349734.png)

### 1. 核心特性

* 1）分片机制提供更好的分布性
  * 同一个索引分成多个分片（sharding），分而治之的方式来提供处理效率。
* 2）横向扩展性
  * 只需要增加一台服务器，做一点配置，启动一下ES进程就可以并入集群,本身扩展性很好，可以扩展到上百台服务器，处理PB级别的数据。
* 3）倒排索引
  * Elasticsearch最关键的就是提供强大的索引能力。**【一切设计都是为了提高搜索的性能】**	
    * 每个字段**field**都编入索引，使其可以被搜索
    * 每个字段所对应的value都被称作**Term**
    * 而这条数据在存入进来的时候会被定义一个**ID**，根据Term会去将ID整合成一个list，被称作**posting list**，存储了所有符合某个Term的文档ID。
  * 在查询的时候，ES先去定位Term，为了能快速找到某个Term，将所有的Term排个序，二分法查找Term，logN的查找效率，就像通过字典查找一样，这就是**Term Dictionary**。
    * ES会直接通过内存查找term，不读磁盘，但是如果term太多，term dictionary也会很大，放内存不现实，于是有了**Term Index**。
    * Term index会和term之间建立映射关系，结合**FST**压缩技术，可以使term index缓存到内存中。
    * 从term index查到对应的term dictionary的block位置之后，再去磁盘上找term，大大减少了磁盘随机读的次数。
    * posting list也需要被压缩，要求posting list是有序的(为了提高搜索的性能，再任性的要求也得满足)，这样做的一个好处是方便压缩。**原理就是将大数变成小数**，最终按位对应成相应的**bitmap**
  * 其中还囊括了**联合索引**，多个field索引的联合查询。
    * 利用跳表(Skip list)的数据结构快速做“与”运算，如果使用跳表，对最短的posting list中的每个id，逐个在另外两个posting list中查找看是否存在，最后得到交集的结果。
    * 利用上面提到的bitset按位“与”，如果使用bitset，就很直观了，直接按位与，得到的结果就是最后的交集。

```sql
# Elasticsearch的索引思路
'''
将磁盘里的东西尽量搬进内存，减少磁盘随机读取次数(同时也利用磁盘顺序读特性)，结合各种压缩算法，用及其苛刻的态度使用内存。
'''

# ⚠️注意
'''
	* 不需要索引的字段，一定要明确定义出来，因为默认是自动建索引的
	* 同样的道理，对于String类型的字段，不需要analysis的也需要明确定义出来，因为默认也是会analysis的
	* 选择有规律的ID很重要，随机性太大的ID(比如java的UUID)不利于查询
'''
```

* 4）数据存储
  * 效仿lucene
    * lucene内部的数据是由一个个segment组成的，写入lucene的数据并不直接落盘，而是先写在内存中，经过了refresh间隔，lucene才将该时间段写入的全部数据refresh成一个segment，segment多了之后会进行merge成更大的segment。lucene查询时会遍历每个segment完成。由于lucene* 写入的数据是在内存中完成，所以写入效率非常高。但是也存在丢失数据的风险，所以Elasticsearch基于此现象实现了translog，只有在segment数据落盘后，Elasticsearch才会删除对应的translog。

| Name                | Extension        | Brief Description                                            |
| :------------------ | :--------------- | :----------------------------------------------------------- |
| Segment Info        | .si              | segment的元数据文件                                          |
| Compound File       | .cfs, .cfe       | 一个segment包含了如下表的各个文件，为减少打开文件的数量，在segment小的时候，segment的所有文件内容都保存在cfs文件中，cfe文件保存了lucene各文件在cfs文件的位置信息 |
| Fields              | .fnm             | 保存了fields的相关信息                                       |
| Field Index         | .fdx             | 正排存储文件的元数据信息                                     |
| Field Data          | .fdt             | 存储了正排存储数据，写入的原文存储在这                       |
| Term Dictionary     | .tim             | 倒排索引的元数据信息                                         |
| Term Index          | .tip             | 倒排索引文件，存储了所有的倒排索引数据                       |
| Frequencies         | .doc             | 保存了每个term的doc id列表和term在doc中的词频                |
| Positions           | .pos             | Stores position information about where a term occurs in the index 全文索引的字段，会有该文件，保存了term在doc中的位置 |
| Payloads            | .pay             | Stores additional per-position metadata information such as character offsets and user payloads 全文索引的字段，使用了一些像payloads的高级特性会有该文件，保存了term在doc中的一些高级特性 |
| Norms               | .nvd, .nvm       | 文件保存索引字段加权数据                                     |
| Per-Document Values | .dvd, .dvm       | lucene的docvalues文件，即数据的列式存储，用作聚合和排序      |
| Term Vector Data    | .tvx, .tvd, .tvf | Stores offset into the document data file 保存索引字段的矢量信息，用在对term进行高亮，计算文本相关性中使用 |
| Live Documents      | .liv             | 记录了segment中删除的doc                                     |

* 5）Elasticsearch SQL

  * 它允许针对Elasticsearch实时执行类似SQL的查询

  * 两种SQL查询

    * ```sql
      POST /_sql?format=txt
      {
        "query": "SELECT account_number,address,age,balance FROM account LIMIT 10"
      }
      ```

    * ```sql
      POST /_sql/translate
      {
        "query": "SELECT account_number,address,age,balance FROM account WHERE age>32 LIMIT 10"
      }
      ```

### 2. 问题

* 1）不支持事务
* 2）各节点数据的一致性问题
  * 其默认的机制是通过多播机制，同步元数据信息，但是在比较繁忙的集群中，可能会由于网络的阻塞，或者节点处理能力达到饱和，导致各数据节点数据不一致——也就是所谓的脑裂问题，这样会使得集群处于不一致状态。
* 3）使用SQL查询ES有一定的局限性，没有原生的Query DSL那么强大，对于嵌套属性和某些函数的支持并不怎么好



## 三、 Doris

```sql
'''
Doris是一个MPP的OLAP系统，主要整合了 Google Mesa（数据模型），Apache Impala（MPP Query Engine) 和 Apache ORCFile (存储格式，编码和压缩) 的技术。
'''
```

### 1. 整体架构

![在这里插入图片描述](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/202201081933252.png)

* 1）Frontend（FE）
  * Doris 系统的元数据和调度节点。在导入流程中主要负责导入规划生成和导入任务的调度工作。
  * FE 主要有有三个角色，一个是 leader，一个是 follower，还有一个 observer。
  * leader 跟 follower，主要是用来达到元数据的高可用，保证单节点宕机的情况下，元数据能够实时地在线恢复，而不影响整个服务。
  * Observer 只是用来扩展查询节点，就是说如果在发现集群压力非常大的情况下，需要去扩展整个查询的能力，那么可以加 observer 的节点。observer 不参与任何的写入，只参与读取。
* 2）Backend（BE）
  * Doris 系统的计算和存储节点。在导入流程中主要负责数据的 ETL 和存储。
  * 数据的可靠性由 BE 保证，BE 会对整个数据存储多副本或者是三副本。副本数可根据需求动态调整。
* 3）FE,BE 都可线性扩展。

### 2. 核心特点

* 1）一些内部组件
  * **Broker**
    * Broker 为一个独立的无状态进程。封装了文件系统接口，提供 Doris 读取远端存储系统中文件的能力，包括HDFS，S3，BOS等
  * **Mysql Client**
    * Doris 借助 MySQL 协议，用户使用任意 MySQL 的 ODBC/JDBC以及MySQL 的客户端，都可以直接访问 Doris
  * **Doris on ES**
    * Doris-On-ES将Doris的分布式查询规划能力和ES(Elasticsearch)的全文检索能力相结合，提供更完善的OLAP分析场景解决方案：
      * ES中的多index分布式Join查询
      * Doris和ES中的表联合查询，更复杂的全文检索过滤
* 2）Column 可以分为两大类
  * Key 和 Value。从业务角度看，Key 和 Value 可以分别对应维度列和指标列。
* 3）Doris 的数据模型主要分为3类
  - Aggregate
    - 当我们导入数据时，对于 Key 列相同的行会聚合成一行，而 Value 列会按照设置的 AggregationType 进行聚合。
  - Uniq
    - 即 Uniq 模型完全可以用聚合模型中的 REPLACE 方式替代。其内部的实现方式和数据存储方式也完全一样。
  - Duplicate
    - 在某些多维分析场景下，数据既没有主键，也没有聚合需求，全量存储。
  - 底层的数据存储，是按照各自建表语句中，AGGREGATE KEY、UNIQ KEY 和 DUPLICATE KEY 中指定的列进行排序存储的。
* 4）数据存储结构
  * tablet是真正存储在be的物理结构，有多个副本。
  * rowset是对于tablet下的数据集合进行的一次变更的版本，例如数据导入，删除和更新等。一个tablet下可能有多个rowset，doris会进行compaction对相邻版本的rowset进行合并，如果是group或者unique数据模型会进行相应的聚合操作。
  * segment是rowset下面的数据分段，是以文件形式存在
    * Data Region：用于存储各个列的数据信息，这里的数据是按需分page加载的
    * Index Region：各个列的索引数据。
  * column 是segment中列式存储的组织结构，包括data column和 index column。
  * page是列数据的分块，每个列的具体数据存储在page当中。同样分为data page和index page。
* 5）索引
  * **前缀索引**
    * Doris使用的是前缀索引，即在排序的基础上，实现的一种根据给定前缀列，快速查询数据的索引方式。我们将一行数据的前 36 个字节 作为这行数据的前缀索引。当遇到 VARCHAR 类型时，前缀索引会直接截断。
    * 当我们的查询条件（where条件），是前缀索引的前缀时，可以极大的加快查询速度。
    * 正确的选择列顺序，能够极大地提高查询效率
  * **Ordinal 索引**
    * ordinal index 主要作用，根据ordinal（page第一条数据行数）找到每个page的具体物理地址并查询具体数据。
  * **Zone Map索引**
    * zoneMap index分为segment和page，分别存储有当前segment或者page中的min，max，hasnull等信息。适用于范围类型查询数据。
  * **bloomFilter**
    * 数据写入page时，会为当前page根据hash策略生成bloom过滤器，查询时如果没有命中，则表示当前page不存在这个数据。主要适用于“=”或者“in”
  * **bitmap索引**
    * 对segment的所有column字段建立索引，写入数据时会维护一个map结构，记录当前列的所有key值对应的行号。适用于对取值少且固定的例如性别，地址等列的查找。
* 6）数据存储
  * 列数据存储
    * Column的data数据按照Page为单位分块存储，每个Page大小一般为64*1024个字节。Page在存储的位置和大小由ordinal index管理。
* 7）优势
  * 同时高效支持大宽表和多表join查询，以支持复杂查询场景；支持标准化SQL
  * 支持高并发查询场景
  * 具有高效的精准去重能力；
  * 数据查询延迟在秒级/毫秒级，同时支持流式数据和批式数据摄入；支持实时离线数据ETL任务
  * Doris有较多的SQL命令协助运维，比如增加节点，Doris中Add Backend

### 3. Doris VS ClickHouse

|                | 类别                                 | Doris                                         | ClickHouse                             |
| -------------- | ------------------------------------ | --------------------------------------------- | -------------------------------------- |
| 总体架构       | Share-Nothing                        | 是                                            | 是                                     |
|                | 列存                                 | 是                                            | 是                                     |
|                | 架构                                 | 内置分布式协议进行元数据同步                  | 依赖ZooKeeper进行DDL和Replica同步      |
|                | Master/Follower/Observer节点类型     |                                               |                                        |
|                | 事务性                               | 事务保证数据ACID                              | 100万以内原子性，DDL无事务保证         |
|                | 数据规模                             | 单集群 < 10PB                                 | 单集群 < 10PB                          |
|                |                                      |                                               |                                        |
| 导入方式       | Kafka导入                            | 内置支持                                      | 内置支持                               |
|                | HDFS导入                             | 内置支持                                      | 外部通过HTTP接口导入                   |
|                | Spark/Flink导入                      | 内置支持                                      | 外部通过HTTP接口导入                   |
|                | 本地JDBC/HTTP                        | 支持                                          | 支持                                   |
|                | INSERT INTO ... SELECT ...           | 支持                                          | 支持                                   |
|                | 数据格式支持                         | orc/parquet/json                              | 支持多种格式                           |
|                |                                      |                                               |                                        |
| 存储架构       | 数据分区                             | 一级范围分区，多个分区字段                    | 支持多个分区字段                       |
|                | 数据分桶                             | 二级Hash分桶，分桶和节点、磁盘无关            | 支持分片，分片和节点相关               |
|                | 多副本                               | 支持多副本                                    | 支持                                   |
|                | 单节点故障不影响使用                 | 支持手工指定副本数量                          |                                        |
|                | 字段类型                             | 支持结构化数据，不支持半结构化/嵌套的数据类型 | 结构化                                 |
|                | 半结构化（Nested、Array、Map）       |                                               |                                        |
|                | 压缩格式                             | 压缩格式支持，LZO，LZ4                        | LZ4，ZSTD，压缩率高                    |
|                | 索引                                 | 前缀索引，36个字节的前缀索                    | 稀疏索引，无限制                       |
|                | 物化视图                             | 支持，自动选择                                | 支持，手工选择物化视图                 |
|                | 精确去重                             | 支持，支持Bitmap                              | 支持                                   |
|                | 近似去重                             | 支持，支持HLL                                 | 支持，uniq函数支持多种去重算法         |
|                | 增量数据合并                         | 支持，后台合并                                | 支持                                   |
|                | 自动分区                             | 支持自动分区（TTL）                           | 支持，支持表、分区、列级别的TTL        |
|                | 数据类型转换                         | 支持                                          | 部分支持                               |
|                | 数据更新与删除                       | 支持，                                        | 支持，Replacing/AggregatingMergeTree   |
|                | Unique Key/Aggregate Key/Primary Key |                                               |                                        |
|                | 写入性能                             | 24-54M/S/Tablet（可加大并发）                 | HDD 150M/S, SSD 250M/S                 |
|                |                                      |                                               |                                        |
|                |                                      |                                               |                                        |
| 计算能力       | SQL兼容性                            | 较好                                          | 差                                     |
|                | 并发能力                             | 并发能力 100QPS/节点，通过增加副本增加并发    | 100QPS/节点，通过增加副本增加并发      |
|                | 宽表与Join                           | 宽表、大小表性能差，大表Join性能好            | 宽表、大小表Join性能优，大表Join性能差 |
|                | 编译执行                             | 支持                                          | 不支持                                 |
|                | 向量化计算                           | 支持                                          | 支持                                   |
|                | 谓词下推                             | 强                                            | 稍弱                                   |
|                | BroadCast Join                       | 支持                                          | 支持                                   |
|                | Shuffle Join                         | 支持                                          | 不支持                                 |
|                | Colocate Join                        | 支持                                          | 支持                                   |
|                | 自定义函数                           | 支持                                          | 暂不支持                               |
|                | 支持结果缓存                         | 支持查询结果缓存                              | 暂不支持，有计划研发                   |
|                | 内存字典                             | 不支持                                        | 支持                                   |
|                | 内存表                               | 不支持                                        | 支持                                   |
|                |                                      |                                               |                                        |
| 扩展性         | 扩展性                               | 协议                                          | 支持JDBC/ODBC协议                      |
|                | 标准SQL                              | 兼容标准SQL                                   | 兼容性稍差                             |
|                | 容器部署                             | 不支持                                        | 支持                                   |
|                | 外表                                 | 外查MySQL/ES/Hive的表                         | 支持MySQL/Hive的表                     |
|                |                                      |                                               |                                        |
| 管理性         | 元数据自动同步                       | 支持                                          | 不支持                                 |
|                | 副本自动均衡                         | 支持                                          | 不支持                                 |
|                | 数据备份                             | 支持                                          | 不支持                                 |
|                | 监控和报警                           | 支持                                          | 支持                                   |
|                | 多租户和资源隔离                     | 弱                                            | 支持，强                               |
|                | 集群间迁移数据                       | 弱                                            | 强，Remote或Copier                     |
|                |                                      |                                               |                                        |
| 权限/安全/审计 | 权限                                 | 支持                                          | 支持，强                               |
|                | 审计                                 | 支持                                          | 支持，强                               |
|                | 端到端加密                           | 暂无                                          | 暂无                                   |
|                | 网络隔离                             | 暂无                                          | 暂无                                   |
|                |                                      |                                               |                                        |
| 两者都有的局限 | 无法高频插入                         |                                               |                                        |
|                | Update/Delete效率不高                |                                               |                                        |
|                | DDL/DML操作后台异步                  |                                               |                                        |
|                | 跨数据中心数据一致性不完善           |                                               |                                        |
|                |                                      |                                               |                                        |





## 四、TiDB

```sql
'''
TiDB是一个开源的NewSQL数据库，它是一款支持混合事务处理和分析处理（ HTAP: Hybrid Transactional/Analytical Processing）的融合型数据库产品，实现了一键水平伸缩，强一致性的多副本数据安全，分布式事务，实时 OLAP 等重要特性。同时兼容 MySQL 协议和生态，迁移便捷，运维成本极低。

NewSQL 健值数据库
'''
```

### 1. 整体架构

![image-20220108171904644](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/202201081933646.png)

* 1）TiDB Server

  * TiDB Server 负责接收 SQL 请求，处理 SQL 相关的逻辑，并通过 PD 找到存储计算所需数据的 TiKV 地址，与 TiKV 交互获取数据，最终返回结果。 TiDB Server 是无状态的，其本身并不存储数据，只负责计算，可以无限水平扩展

* 2）PD Server

  * Placement Driver是整个集群的管理模块。
    * 存储集群的元信息（某个 Key 存储在哪个 TiKV 节点）
    * 对 TiKV 集群进行调度和负载均衡
    * 分配全局唯一且递增的事务 ID

* 3）TiKV Server

  * TiKV Server 负责存储数据，从外部看 TiKV 是一个分布式的提供事务的 Key-Value 存储引擎。

  * 存储数据的基本单位是 Region（区域），每个 Region 负责存储一个 Key Range （从 StartKey 到 EndKey 的左闭右开区间）的数据。

  * 每个 TiKV 节点会负责多个 Region 。

  * TiKV 使用 Raft 协议做复制，保持数据的一致性和容灾。

  * 副本以 Region 为单位进行管理，不同节点上的多个 Region 构成一个 Raft Group，互为副本。![截屏2022-01-02 下午1.59.41](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/202201081932005.png)

  * 数据在多个 TiKV 之间的负载均衡由 PD 调度，这里也是以 Region 为单位进行调度。

  * 数据存储：

    * Percolator 提供三个 column family (CF)，Lock，Data 和 Write，当写入一个 key-value 的时候，会将这个 key 的 lock 放到 Lock CF 里面，会将实际的 value 放到 Data CF 里面，如果这次写入 commit 成功，则会将对应的 commit 信息放到入 Write CF 里面。
    * Key 在 Data CF 和 Write CF 里面存放的时候，会把对应的时间戳给加到 Key 的后面。在 Data CF 里面，添加的是 startTS，而在 Write CF 里面，则是 commitCF。
    * TiKV 会将数据存储到 RocksDB，RocksDB 是一个 key-value 存储系统
    * 每个 TiKV 包含两个 RocksDB 实例，一个用于存储 Raft Log，另一个则是存放用户实际的数据
    * 一个 TiKV 会有多个 Regions，我们在 Raft RocksDB 里面会使用 Region 的 ID 作为 key 的前缀，然后再带上 Raft Log ID 来唯一标识一条 Raft Log

    ![截屏2022-01-02 下午1.55.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/202201081932483.png)

### 2. 核心特性

* 1）无限水平扩展：计算能力和存储能力

  * TiDB Server 负责处理 SQL 请求，随着业务的增长，可以添加 TiDB Server 节点，提高整体的处理能力，提供更高的吞吐。
  * TiKV 负责存储数据，随着数据量的增长，可以部署更多的 TiKV Server 节点解决数据 Scale 的问题。
  * PD 会在 TiKV 节点之间以 Region 为单位做调度，将部分数据迁移到新加的节点上。
  * 所以在业务的早期，可以只部署少量的服务实例，随着业务量的增长，按照需求添加 TiKV 或者 TiDB 实例。

* 2）高度兼容MySQL

  * 直接可以从MySQL迁移至TiDB，分库分表后的MySQL集群也可以通过TiDB工具进行实时迁移
  * 用户可以透明地从MySQL切换到TiDB

* 3）支持分布式事务

  * 100%支持标准的ACID事务，支持Flink ExactlyOnce语意，支持幂等
  * 乐观事务模型--Percolator算法模型
    * 乐观事务的冲突检测主要是在prewrite阶段，如果检测到当前的key已经加锁，会有一个等待时间，这个时间过后如果还没有获取到锁，就返回失败。因此当多个事务修改同一个key时，必然导致大量的锁冲突。

  ![img](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/img/202201081932516.png)

* 4）一站式HTAP解决方案

### 3. 问题

* 1）无向量化执行
* 2）无预聚合功能
* 3）无预计算手段
* 4）OLAP 性能相对 ClickHouse 或 Doris 较弱，因为TiDB还是行式存储。
* 5）相较于MysQL对超大表的join没有太多的性能提升，需要借助TiSpark















