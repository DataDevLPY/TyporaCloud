

## Standalone模式



```shell
# 开启集群
(base) peiyang@Sunny-Mac-Pro flink-1.10.1 % ./bin/start-cluster.sh


(base) peiyang@Sunny-Mac-Pro flink-1.10.1 % jps

## browser -- localhost:8081


# 关闭集群
(base) peiyang@Sunny-Mac-Pro flink-1.10.1 % ./bin/stop-cluster.sh


# 改变 slots的个数
(base) peiyang@Sunny-Mac-Pro flink-1.10.1 % vi ./conf/flink-conf.yaml 

```

* 从终端直接提交Jar包

```shell
# 提交、设置并行
./bin/flink run -c com.peiyang.wc.StreamWordCountHost -p 2 /Users/peiyang/IdeaProjects/FlinkTurtorial/target/FlinkTurtorial-1.0-SNAPSHOT.jar --host localhost --port 7777

# 查看任务编号
./bin/flink list

# 取消任务
./bin/flink cancel 任务编号


--host localhost --port 7777
```



## Yarn 模式

![截屏2021-07-24 下午4.28.29](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220124451.png?token=AWS37JNF2M4KFVSPW3MXRA3BTKAQS) 

![截屏2021-07-24 下午4.30.57](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220124298.png?token=AWS37JLUYKHW3LSAE2UZBQTBTKAQY)

![截屏2021-07-24 下午4.30.57](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220124020.png?token=AWS37JM33T764PTU4L4ZWTLBTKARO)

![截屏2021-07-24 下午4.48.40](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220119280.png?token=AWS37JKBQYW25OGFVXBFCQDBTJ74I)











