## AspectJ注解

1. 创建类，在类里面定义方法





2. 创建增强类（编写增强逻辑）

（1）在增强类里面，创建方法，让不同方法代表不同通知类型





3. 进行通知的配置

（1）在spring配置文件中 开启注解扫描

```xml
<!--开启注解扫描-->
    <context:component-scan base-package="cn.peiyang.spring.aopAnno"> </context:component-scan>
```



（2）使用注解创建User和UserProxy对象

```java
package cn.peiyang.spring.aopAnno;

import org.springframework.stereotype.Component;

@Component
public class UserProxy {
    
    public void before(){
        System.out.println("before....");
    }

}

```



```java
package cn.peiyang.spring.aopAnno;

import org.springframework.stereotype.Component;

@Component
public class User {
    public void add(){
        System.out.println("add.....");
    }
}
```



（3）在增强类上面添加注解@Aspect



（4）在spring配置文件中开启生成代理对象

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">


    <!--开启注解扫描-->
    <context:component-scan base-package="cn.peiyang.spring.aopAnno"> </context:component-scan>

    <!--开启生成代理对象-->
    <aop:aspectj-autoproxy> </aop:aspectj-autoproxy>



</beans>

```



4. 配置不同类型的通知

（1）在增强类的里面，在座位通知方法上面 添加通知类型注解，使用切入点表达式配置

```java
package cn.peiyang.spring.aopAnno;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component
@Aspect     //生成代理对象
public class UserProxy {

    //前置通知
    @Before(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void before(){
        System.out.println("before....");
    }

    //后置通知
    @After(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void after(){
        System.out.println("after....");
    }

    //最终通知
    @AfterReturning(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void afterReturning(){
        System.out.println("afterReturning....");
    }

    //异常通知
    @AfterThrowing(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void afterThrowing(){
        System.out.println("afterThrowing....");
    }


    //环绕通知
    @Around(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("环绕之前....");

        //被增强的方法执行
        proceedingJoinPoint.proceed();

        System.out.println("环绕之后....");
    }
}
```





```java
package cn.peiyang.spring.aopTest;

import cn.peiyang.spring.aopAnno.User;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AopTest {

    @Test
    public void test1(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        User user = context.getBean("user",User.class);

        user.add();
    }

}

```



![截屏2021-07-08 下午9.00.40](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-08 下午9.00.40.png)





5. 公共 切入点抽取

```java
package cn.peiyang.spring.aopAnno;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

@Component
@Aspect     //生成代理对象
public class UserProxy {

    //相同切入点抽取
    @Pointcut(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void pointDemo(){

    }

    //前置通知
    @Before(value="pointDemo()")
    public void before(){
        System.out.println("before....");
    }

    //后置通知
    @After(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void after(){
        System.out.println("after....");
    }

    //最终通知
    @AfterReturning(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void afterReturning(){
        System.out.println("afterReturning....");
    }

    //异常通知
    @AfterThrowing(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void afterThrowing(){
        System.out.println("afterThrowing....");
    }


    //环绕通知
    @Around(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("环绕之前....");

        //被增强的方法执行
        proceedingJoinPoint.proceed();

        System.out.println("环绕之后....");
    }

}
```



6. 有多个增强类对同一个方法进行增强，设置增强类优先级

（1）在增强类上面添加注解@Order（数字类型值），数字类型值越小优先级越高

```java
package cn.peiyang.spring.aopAnno;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

@Component
@Aspect
@Order(0)
public class PersonProxy {

    //前置通知
    @Before(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void before(){

        System.out.println("Person before....");
    }
}

```



```java
package cn.peiyang.spring.aopAnno;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.core.annotation.Order;
import org.springframework.stereotype.Component;

@Component
@Aspect     //生成代理对象
@Order(1)
public class UserProxy {

    //相同切入点抽取
    @Pointcut(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void pointDemo(){

    }

    //前置通知
    @Before(value="pointDemo()")
    public void before(){
        System.out.println("before....");
    }

    //后置通知
    @After(value="execution(* cn.peiyang.spring.aopAnno.User.add(..))")
    public void after(){
        System.out.println("after....");
    }
    
}

```



7. 完全配置类

```java
package cn.peiyang.spring.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@ComponentScan(basePackages = {"cn.peiyang.spring"})
@EnableAspectJAutoProxy(proxyTargetClass = true)
public class aopConfig {
}
```

