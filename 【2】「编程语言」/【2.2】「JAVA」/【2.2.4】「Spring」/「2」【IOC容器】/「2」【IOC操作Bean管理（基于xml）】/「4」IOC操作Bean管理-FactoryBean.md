## IOC操作Bean管理（FactoryBean）

1. Spring有两种类型bena，一种普通bean，另外一种工厂bean（FactoryBean）
2. 普通bean：在配置文件中定义bean类型就是返回类型
3. 工厂bean：在配置文件定义bean类型可以和返回类型不一样

第一步 创建类，让这个类作为工厂bean，实现接口FactoryBean

第二部 实现接口里面的方法，在实现的方法中定义返回的bean类型

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myBean" class="cn.peiyang.spring.factoryBean.MyBean"> </bean>

</beans>
```

```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.collectionType.Book;
import cn.peiyang.spring.collectionType.Course;
import cn.peiyang.spring.collectionType.Stud;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class collectionTest {

    @Test
    public void FacTest(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean3.xml");

        Course course = context.getBean("myBean", Course.class);
        System.out.println(course);
    }

}

```



```java
package cn.peiyang.spring.collectionType;

import lombok.Setter;

@Setter
public class Course {
     private String cname;

    @Override
    public String toString() {
        return "Course{" +
                "cname='" + cname + '\'' +
                '}';
    }
}

```



```java
package cn.peiyang.spring.factoryBean;

import cn.peiyang.spring.collectionType.Course;
import org.springframework.beans.factory.FactoryBean;

public class MyBean implements FactoryBean<Course> {

    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        course.setCname("abc");
        return course;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }

    @Override
    public boolean isSingleton() {
        return FactoryBean.super.isSingleton();
    }
}

```



## 