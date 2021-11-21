

### 1、基于xml方式创建对象

（1）在spring配置文件中，使用bean标签，标签里面添加对应属性，就可以实现对象创建

（2）在bean标签有很多属性，介绍常用的属性

* id属性：唯一标识
* class属性：类全路径（包类路径）

（3）创建对象的时候，默认也是执行无参构造方法完成对象创建



### 2、基于xml方式注入属性

（1）DI：依赖注入，就是注入属性

### 3、第一种注入方式：使用set方法进行注入

（1）创建类，定义属性和对应的set方法

```java
package cn.peiyang.spring;

/**
 * 演示使用set进行注入属性
 */

public class Book1 {
    //创建属性
    private String bname;
    private String bauthor;

    //创建属性对应的set方法
    public void setBname(String bname) {
        this.bname = bname;
    }

    public void setBauthor(String bauthor) {
        this.bauthor = bauthor;
    }
    
    public static void main(String[] args) {
        Book book = new Book();
        book.setBname("abc");
    }
}  

```

（2）在spring配置文件配置对象创建，配置属性注入

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--1 配置User对象创建-->
    <!--bean id="user" class="cn.peiyang.spring.User"> </bean-->

    <!--2 set方法注入属性-->
    <bean id="book" class="cn.peiyang.spring.Book1">
        <!--使用property完成属性注入-->
        <property name="bname" value="易筋经"> </property>
        <property name="bauthor" value="达摩老祖"> </property>
    </bean>
</beans>

```

测试：

```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.Book1;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestBook1 {

    @Test
    public void testBook1(){
        //1.加载spring配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        //2。获取配置创建的对象
        Book1 book = context.getBean("book", Book1.class);

        System.out.println(book);
        book.testDemo();
    }

}

```



### 4.第二种注入，有参构造注入

（1）创建类，定义属性、创建属性对应的有参构造

```java
package cn.peiyang.spring;


/**
 * 使用有参构造注入
 */
public class Orders {
    private String oname;
    private String address;

    //有参构造
    public Orders(String oname, String address) {
        this.oname = oname;
        this.address = address;
    }
}

```

（2）在spring配置文件中进行配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--1 配置User对象创建-->
    <!--bean id="user" class="cn.peiyang.spring.User"> </bean-->

    <!--2 set方法注入属性-->

    <!--bean id="book" class="cn.peiyang.spring.Book1"-->
        <!--使用property完成属性注入-->
        <!--property name="bname" value="易筋经"> </property-->
        <!--property name="bauthor" value="达摩老祖"> </property-->
    <!--/bean-->

    <!--3. 有参构造注入属性-->
    <bean id="order" class="cn.peiyang.spring.Orders">
        <constructor-arg name="oname" value="computer"> </constructor-arg>
        <constructor-arg name="address" value="China"> </constructor-arg>
    </bean>


</beans>		
```



### 5. p名称空间注入

（1）使用p名称空间注入，可以简化基于xml的

![B5EFAF2E-0C1B-4421-99F3-8D2336121486](/Users/peiyang/Typora Notebook/【JAVA】/【框架】/【Spring】/B5EFAF2E-0C1B-4421-99F3-8D2336121486.PNG)

