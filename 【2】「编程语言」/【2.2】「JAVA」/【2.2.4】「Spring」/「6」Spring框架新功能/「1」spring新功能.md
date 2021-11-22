## Spring框架新功能



### 日志

![截屏2021-07-11 下午12.22.48](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047580.png?token=AWS37JO2QAEH2SAXK3ZLBK3BTJ4FG)

![截屏2021-07-11 下午12.29.43](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047718.png?token=AWS37JMEHP3ABCZXVI3NOM3BTJ4FQ)

![截屏2021-07-11 下午12.27.11](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047093.png?token=AWS37JJQDD6GAAL4L2ZG5HTBTJ4FU) 

![截屏2021-07-11 下午12.29.03](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047508.png?token=AWS37JLL4WFM5JCZ6BYOG2LBTJ4F4)

![截屏2021-07-11 下午12.30.02](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047457.png?token=AWS37JIGYCSJJLE2GDOHF7LBTJ4GE)

![截屏2021-07-11 下午12.34.55](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047653.png?token=AWS37JPFSHIE5TZNFEHSQM3BTJ4GS)

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



![截屏2021-07-11 下午10.23.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047040.png?token=AWS37JKD2IE63XCSEZDBLDDBTJ4G2) 





![截屏2021-07-11 下午10.32.45](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047889.png?token=AWS37JNASW6JL2IJMOJVVKTBTJ4G6)

![截屏2021-07-11 下午10.32.15](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047445.png?token=AWS37JIGG5EU73ENB72BTN3BTJ4HI)

 ![截屏2021-07-11 下午10.32.30](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047727.png?token=AWS37JPFAMFQRRNE6SA5JZLBTJ4HK)



### 测试









