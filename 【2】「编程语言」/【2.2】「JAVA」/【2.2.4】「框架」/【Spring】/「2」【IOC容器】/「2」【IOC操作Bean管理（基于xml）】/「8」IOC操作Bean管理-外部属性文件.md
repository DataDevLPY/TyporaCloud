## IOC操作Bean管理（外部属性文件）

1. 直接配置数据路信息

（1）配置德鲁伊连接池

​	导入druid-1.1.9.jar

（2）引入德鲁伊连接池依赖jar包

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--直接配置连接池-->
    <bean id="" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"> </property>
        <property name="url" value="cjdbc:mysql://local:3306/userDb"> </property>
        <property name="username" value="root"> </property>
        <property name="password" value="root"> </property>
    </bean>

</beans>
```



2. 引入外部属性文件配置数据库连接池

（1）创建外部属性文件，properties格式文件，写数据库信息

```properties
prop.driverClass=com.mysql.jdbc.Driver
prop.url=jdbc:mysql://localhost:3306/userDb
prop.username=root
prop.password=root
```

（2）把外部properties属性文件引入到spring配置文件中

* 引入context名称空间

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!--直接配置连接池-->
    <bean id="" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"> </property>
        <property name="url" value="cjdbc:mysql://local:3306/userDb"> </property>
        <property name="username" value="root"> </property>
        <property name="password" value="root"> </property>
    </bean>

</beans>
```

* 引入外部属性文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--引入外部属性文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>
    
    <!--配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${prop.driverClass}"> </property>
        <property name="url" value="${prop.url}"> </property>
        <property name="username" value="${prop.username}"> </property>
        <property name="password" value="${prop.password}"> </property>
    </bean>

</beans>
```



