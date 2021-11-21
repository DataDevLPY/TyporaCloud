#  第一章 概述

1. 优点

   1）高容错性

   2）适合处理大数据

   3）可构建在廉价的机器上

2. 缺点

   1）不适合低延时数据访问

   2） 无法高效的对大量小文件进行存储

   3）不支持并发写入、文件随机修改 



# 第二章 HDFS的Shell操作

以hadoop fs开头

以hdfs dfs 开头

**常用**

```
1）-ls: 显示目录信息
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -ls /

2）-mkdir：在HDFS上创建目录
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -mkdir -p /sanguo/shuguo

3）-cat：显示文件内容
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -cat /sanguo/shuguo/kongming.txt

4）-chgrp 、-chmod、-chown：Linux文件系统中的用法一样，修改文件所属权限
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs  -chmod  666  /sanguo/shuguo/kongming.txt
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs  -chown  atguigu:atguigu   /sanguo/shuguo/kongming.txt

5）-cp ：从HDFS的一个路径拷贝到HDFS的另一个路径
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -cp /sanguo/shuguo/kongming.txt /zhuge.txt

6）-mv：在HDFS目录中移动文件
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -mv /zhuge.txt /sanguo/shuguo/

7）-tail：显示一个文件的末尾
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -tail /sanguo/shuguo/kongming.txt

8）-rm：删除文件或文件夹
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -rm /user/atguigu/test/jinlian2.txt

9）-rmdir：删除空目录
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -mkdir /test
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -rmdir /test

10）-du统计文件夹的大小信息
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -du -s -h /user/atguigu/test
2.7 K  /user/atguigu/test
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -du  -h /user/atguigu/test
1.3 K  /user/atguigu/test/README.txt
15     /user/atguigu/test/jinlian.txt
1.4 K  /user/atguigu/test/zaiyiqi.txt

11）-setrep：设置HDFS中文件的副本数量
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -setrep 10 /sanguo/shuguo/kongming.txt


```



**HDFS上传**

```
1）-moveFromLocal：从本地剪切粘贴到HDFS
[atguigu@hadoop102 hadoop-3.1.3]$ touch kongming.txt
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs  -moveFromLocal  ./kongming.txt  /sanguo/shuguo

2）-copyFromLocal：从本地文件系统中拷贝文件到HDFS路径去
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -copyFromLocal README.txt /

3）-appendToFile：追加一个文件到已经存在的文件末尾
[atguigu@hadoop102 hadoop-3.1.3]$ touch liubei.txt
[atguigu@hadoop102 hadoop-3.1.3]$ vi liubei.txt
输入
san gu mao lu
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -appendToFile liubei.txt /sanguo/shuguo/kongming.txt
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -appendToFile - /sanguo/shuguo/kongming.txt

4）-put：等同于copyFromLocal
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -put ./zaiyiqi.txt /user/atguigu/test/

```



**HDFS下载**

```
1）-copyToLocal：从HDFS拷贝到本地
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -copyToLocal /sanguo/shuguo/kongming.txt ./

2）-get：等同于copyToLocal，就是从HDFS下载文件到本地
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -get /sanguo/shuguo/kongming.txt ./

3）-getmerge：合并下载多个文件，比如HDFS的目录 /user/atguigu/test下有多个文件:log.1, log.2,log.3,...
[atguigu@hadoop102 hadoop-3.1.3]$ hadoop fs -getmerge /user/atguigu/test/* ./zaiyiqi.txt

```



# 第三章 HDFS客户端操作





















