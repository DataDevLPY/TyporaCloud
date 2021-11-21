## XML声明式事务管理

![截屏2021-07-11 上午11.14.38](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047444.png?token=AWS37JK6FXH3FLBZKMP4KDLBTJ4EI)

![截屏2021-07-11 上午11.14.52](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220047438.png?token=AWS37JIYNHAKB5OF7PNQKOLBTJ4EQ)



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
                           http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--组件扫描-->
    <context:component-scan base-package="cn.peiyang.tx"> </context:component-scan>
    
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${jdbc.driverClass}"> </property>
        <property name="url" value="${jdbc.url}"> </property>
        <property name="username" value="${jdbc.username}"> </property>
        <property name="password" value="${jdbc.password}"> </property>
    </bean>

    <!--JdbcTemplate对象-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
         <!--注入dataSource-->
        <property name="dataSource" ref="dataSource"> </property>
    </bean>


    <!--创建事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入dataSource-->
        <property name="dataSource" ref="dataSource"> </property>
    </bean>


    <!--配置通知-->
    <tx:advice id="txadvice">
        <!--配置事务参数-->
        <tx:attributes>
            <!--tx:method name="account*"/-->
            <tx:method name="accountMoney" propagation="REQUIRES_NEW"/>
        </tx:attributes>
    </tx:advice>

    <!--配置切入点和切面-->  
    <aop:config>
        <!--配置切入点-->
        <aop:pointcut id="pt" expression="execution(* cn.peiyang.tx.Service.UserService.*(..)"/>
        <!--配置切面-->
        <aop:advisor advice-ref="txadvice" pointcut-ref="pt"></aop:advisor>

    </aop:config>
            

</beans>

```

