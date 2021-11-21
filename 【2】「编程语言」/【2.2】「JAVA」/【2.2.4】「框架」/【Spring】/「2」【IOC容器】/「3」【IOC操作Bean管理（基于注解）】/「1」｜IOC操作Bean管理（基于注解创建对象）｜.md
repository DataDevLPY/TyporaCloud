IOC操作Bean管理（基于注解方式）

1. 什么是注解

（1）注解是代码特殊标记，格式：@注解名称

（2）使用注解，注解作用在类上面，方法上面，属性上面

（3）使用注解目的：简化xml配置

2. Spring针对Bean管理中创建对象提供注解

（1）@Component

（2）@Service

（3）@Controller

（4）@Repository

* 上面四个注解功能是一样的，都可以用来创建bean实例

3. 基于注解方式实现对象创建

（1）第一步引入依赖

spring-aop-5.2.6.RELRASE.jar

（2）第二部 开启组件扫描

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!--开启组件扫描
        1 如果扫描多个包，多个包使用逗号隔开
        2 扫描包上层目录
    -->
    <context:component-scan base-package="cn.peiyang.spring"></context:component-scan>

</beans>
```



```java
package cn.peiyang.spring.service;


import org.springframework.stereotype.Component;


//在注解里面value属性值可以省略不写
//默认值是类名称，首字母小写
//UserService -- userService

@Component  //<bean id="userService" class=""/>
public class UserService {

    public void add(){
        System.out.println("service add.....");
    }
}
```



```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.service.UserService;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class collectionTest {

    @Test
    public void userServiceTest(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

        UserService userService = context.getBean("userService", UserService.class);
        System.out.println(userService);
        userService.add();
    }

}
```



4. 开启组件扫描细节配置

```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.service.UserService;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class collectionTest {

    @Test
    public void userServiceTest(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

        UserService userService = context.getBean("userService", UserService.class);
        System.out.println(userService);
        userService.add();
    }

}
```

```java
package cn.peiyang.spring.service;


import org.springframework.stereotype.Component;


//在注解里面value属性值可以省略不写
//默认值是类名称，首字母小写
//UserService -- userService

@Component  //<bean id="userService" class=""/>
public class UserService {

    public void add(){
        System.out.println("service add.....");
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!--开启组件扫描
        1 如果扫描多个包，多个包使用逗号隔开
        2 扫描包上层目录
    -->


    <!--示例1
        use-default-filters = "false" 表示现在不使用默认filter，自己配置filter
        context:include-filter，设置扫描内容
    -->
    <context:component-scan base-package="cn.peiyang.spring" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Service"/>
    </context:component-scan>

    <!--示例2
        context:include-exclude，设置不扫描内容
    -->
    <!--
    <context:component-scan base-package="cn.peiyang.spring">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Component"/>
    </context:component-scan>
    -->
</beans>
```







