

## 一、依赖拉取本地mvn

```shell
mvn install:install-file -DgroupId=com.jd.security.codesec -DartifactId=ajdv -Dversion=0.1 -Dpackaging=jar -Dfile=ajdv-0.1.jar
```



## 二、mvn编译

```shell
mvn clean package -Dmaven.test.skip=true
--编译
mvn compile
--执行main方法
mvn exec:java -Dexec.mainClass="com.xxx.demo.Hello"
```



## 三、Maven常用命令





## 四、Maven命令参数

```
-D传入属性参数
mvn clean package -Dmaven.test.skip=true

以-D开头，将maven.test.skip的值设为true，就是告诉maven打包的时候跳过单元测试。
同理，mvn deploy-Dmaven.test.skip=true代表部署项目并跳过单元测试

-P使用指定的Profile配置
```

















