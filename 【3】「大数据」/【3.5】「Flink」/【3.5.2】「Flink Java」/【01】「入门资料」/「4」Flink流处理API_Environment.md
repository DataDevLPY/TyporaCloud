

## 环境

### getExecutionEnvironment

![截屏2021-07-24 下午11.18.51](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220125992.png?token=AWS37JMRBGODJSGQNXKWN2DBTKAVK)

```java
ExecutionEnvironment env = ExecutionEnvironment.getExecutionEnvironment();

StreamExecutionEnvironment env = SteamExecutionEnvironment.getExecutionEnvironment();
```

![截屏2021-07-24 下午11.20.43](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220125800.png?token=AWS37JPXX7QXP45LLJSLKCDBTKAVO)



### createLocalEnvironment

​		返回本地执行环境，需要在调用时指定默认的并行度

```java
LocalStreamEnvironment env = StreamExecutionEnvironment.createLocalEnvironment(1);
```



### createRemote Environment

​		返回集群执行环境，将Jar提交到远程服务器。需要在调用时指定JobManager的IP和端口号，并制定要在集群中运行的Jar包。

```java
StreamExecutionEnvironment env = StreamExecutionEnvironment.createRemoteEnvironment("jobmanage-hostname",6123,"YOURPATH//WordCount.jar");
```





