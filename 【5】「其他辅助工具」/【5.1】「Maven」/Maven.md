

B/S 

浏览器/服务端 

Broaden/Service



```
mvn clean package -Dmaven.test.skip=true
--编译
mvn compile
```



```
--执行main方法
mvn exec:java -Dexec.mainClass="com.xxx.demo.Hello"
```



## Maven常用命令





## Maven命令参数

```
-D传入属性参数
mvn clean package -Dmaven.test.skip=true

以-D开头，将maven.test.skip的值设为true，就是告诉maven打包的时候跳过单元测试。
同理，mvn deploy-Dmaven.test.skip=true代表部署项目并跳过单元测试


```

```
-P使用指定的Profile配置


```

















