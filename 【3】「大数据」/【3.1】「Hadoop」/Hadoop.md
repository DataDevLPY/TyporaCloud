看日志 ogs/logs 

1. 搭完集群，格式化成功，启动失败，重新格式化，启动又失败

   关掉集群，三台机器都删除数据

```
cd $HADOOP_HOME
rm -rf data logs
hdfs namenode -format


start-dfs.sh
```



2. xsync, 启动集群, host unreachable, can't resolve hostname, Unknown host exception

```
/etc/hosts 文件配置不正确
```



3. Permission denied, 权限拒绝

   权限不足

   a. 在不属于你的文件夹下进行操作

   b. 进行系统级操作

   ​	systemctl start network 需要root权限

    	修改环境变量

   以上两种加sudo是正常的 

​		c. 如果module文件夹出了权限问题

​		改拥有者

​		sudo chown peiyang:peiyang /opt/module

---------------

**core-site.xml**

```
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop102:8020</value>
    </property>
    <property>
        <name>hadoop.data.dir</name>
        <value>/opt/module/hadoop-3.2.2/data</value>
    </property>
    <property>
        <name>hadoop.proxyuser.peiyang.hosts</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.peiyang.groups</name>
        <value>*</value>
    </property>
</configuration>
```



**hdfs-site.xml**

```
<configuration>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file://${hadoop.data.dir}/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file://${hadoop.data.dir}/data</value>
    </property>
    <property>
        <name>dfs.namenode.checkpoint.dir</name>
        <value>file://${hadoop.data.dir}/namesecondary</value>
    </property>
    <property>
        <name>dfs.client.datanode-restart.timeout</name>
        <value>30</value>
    </property>
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>hadoop104:9868</value>
    </property>
</configuration>
```



**mapred-site.xml**

```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>hadoop102:10020</value>
    </property>
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>hadoop102:19888</value>
    </property>

</configuration>
```



**yarn-site.xml**

```
<configuration>

<!-- Site specific YARN configuration properties -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>hadoop103</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>       <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
    </property>
    <property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
    </property>
    <property>
        <name>yarn.log.server.url</name>
        <value>http://hadoop102:19888/jobhistory/logs</value>
    </property>
    <property>
        <name>yarn.log-aggregation.retain-seconds</name>
        <value>604800</value>
    </property>
</configuration>
```



**workers.sh**

```
hadoop102
hadoop103
hadoop104
```



--------

看端口

netstat -nltp

netstat -nltp | grep 4380



看进程

ps -ef



安装包

yum provides tree

yum install -y iotop tree





查看剩余内存

free -h



查看分区、磁盘大小

df -h

查看文件夹占了多少

du -h /etc

```
subject name grade
```

