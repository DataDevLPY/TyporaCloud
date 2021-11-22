## IOC Bean管理（xml自动装配）

1. 手动装配

```java
package cn.peiyang.spring.autowire;


import lombok.Setter;

@Setter
public class Emp {
    private Dept dept;


    @Override
    public String toString() {
        return "Emp{" +
                "dept=" + dept +
                '}';
    }

    public void test(){
        System.out.println(dept);
    }
}
```

```java
package cn.peiyang.spring.autowire;

public class Dept {
    @Override
    public String toString() {
        return "Dept{}";
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="emp" class="cn.peiyang.spring.autowire.Emp">
        <property name="dept" ref="dept">
        </property>
    </bean>
    <bean id="dept" class="cn.peiyang.spring.autowire.Dept"> </bean>
</beans>
```

```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.autowire.Emp;
import cn.peiyang.spring.bean.Orders;
import cn.peiyang.spring.collectionType.Book;
import cn.peiyang.spring.collectionType.Course;
import cn.peiyang.spring.collectionType.Stud;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class collectionTest {
    @Test
    public void empTest(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean5.xml");

        Emp emp = context.getBean("emp", Emp.class);
        System.out.println(emp);
    }
}
```



2. 自动装配

（1）根据指定装配规则（属性名称或者属性类型），spring自动将匹配的属性值进行注入



2. 演示自动装配过程

（1）根据属性名称自动注入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    
    <!--实现自动装配
        bean标签属性autowire，配置自动装配
        autowire属性常用两个值：
            byName根据属性名称注入，注入值bean的id值和类属性名称一样
            byType根据属性类型注入
    -->
    <bean id="emp" class="cn.peiyang.spring.autowire.Emp" autowire="byName">
        <!--property name="dept" ref="dept">
        </property-->
    </bean>
    <bean id="dept" class="cn.peiyang.spring.autowire.Dept"> </bean>
</beans>

```



（2）根据属性类型自动注入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    
    <!--实现自动装配
        bean标签属性autowire，配置自动装配
        autowire属性常用两个值：
            byName根据属性名称注入，注入值bean的id值和类属性名称一样
            byType根据属性类型注入
    -->
    <bean id="emp" class="cn.peiyang.spring.autowire.Emp" autowire="byType">
        <!--property name="dept" ref="dept">
        </property-->
    </bean>
    <bean id="dept" class="cn.peiyang.spring.autowire.Dept"> </bean>
</beans>
```

