## Spring框架新功能



### 日志

![截屏2021-07-11 下午12.22.48](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午12.22.48.png)

![截屏2021-07-11 下午12.29.43](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午12.29.43.png)

![截屏2021-07-11 下午12.27.11](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午12.27.11.png) 

![截屏2021-07-11 下午12.29.03](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午12.29.03.png)

![截屏2021-07-11 下午12.30.02](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午12.30.02.png)

![截屏2021-07-11 下午12.34.55](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午12.34.55.png)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--日志级别以及优先级排序：OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL-->
<!--Configuration后面的status用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，可以看到log4j2内部各种详细输出-->
<configuration status="INFO">
    <!--先定义所有的appenders-->
    <appenders>
        <!--输出日志信息到控制台-->
        <console name="Console" target="SYSTEM_OUT">
            <!--控制日志输出的格式-->
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </console>
    </appenders>
    <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    <!--root：用于指定项目的根日志，如果没有单独制定Logger，则会使用root作为默认的日志输出-->
    <loogers>
        <root level="info">
            <appender-ref ref="Console"/>
        </root>
    </loogers>
</configuration>


```



### @Nullable



![截屏2021-07-11 下午10.23.05](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午10.23.05.png) 





![截屏2021-07-11 下午10.32.45](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午10.32.45.png)

![截屏2021-07-11 下午10.32.15](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午10.32.15.png)

 ![截屏2021-07-11 下午10.32.30](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-11 下午10.32.30.png)



### 测试

![8671626056076_.pic_hd](/Users/peiyang/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/46270fd164b1a21483416e93c94fe200/Message/MessageTemp/770716673a126538c5fb1ca0e713e060/Image/8671626056076_.pic_hd.jpg)

![8681626056107_.pic_hd](/Users/peiyang/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/46270fd164b1a21483416e93c94fe200/Message/MessageTemp/770716673a126538c5fb1ca0e713e060/Image/8681626056107_.pic_hd.jpg)

![8691626056141_.pic_hd](/Users/peiyang/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/46270fd164b1a21483416e93c94fe200/Message/MessageTemp/770716673a126538c5fb1ca0e713e060/Image/8691626056141_.pic_hd.jpg)

![8701626056168_.pic_hd](/Users/peiyang/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/46270fd164b1a21483416e93c94fe200/Message/MessageTemp/770716673a126538c5fb1ca0e713e060/Image/8701626056168_.pic_hd.jpg)











