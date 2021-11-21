# Spring框架概述



![截屏2021-07-04 下午1.30.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-04 下午1.30.01.png)



		1. Spring是轻量级的开源的JavaEE的框架
  		2. Spring可以解决企业应用开发的复杂性
  		3. Sping有两个核心部分：IOC和Aop



![截屏2021-07-04 下午3.49.02](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-04 下午3.49.02.png)



**简单模拟**

```java
package cn.peiyang.spring;

public class User {
    public void add(){
        System.out.println("add...");
    }
}
```



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--配置User对象创建-->
    <bean id="user" class="cn.peiyang.spring.User"> </bean>
</beans>

```



```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.User;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestSpring {

    @Test
    public void testAdd(){
        //1,加载Spring配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

        //2.获取配置文件创建的对象
        User user = context.getBean("user", User.class);

        System.out.println(user);
        user.add();

    }

}
```

