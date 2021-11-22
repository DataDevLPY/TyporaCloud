## IOC操作Bean管理（xml注入集合属性）

1、注入数组类型属性

2、注入List集合类型属性

3、注入Map集合类型属性



（1）创建类，定义数组、list、map、set

```java
package cn.peiyang.spring.collectionType;

import lombok.Setter;

import java.util.List;
import java.util.Map;
import java.util.Set;

@Setter
public class Stud {

    //1 数组类型属性
    private String[] courses;

    //2 list集合类型属性
    private List<String> list;

    //3 map集合类型属性
    private Map<String,String> map;

    //4 set集合类型属性
    private Set<String> sets;

}

```



（2）在spring配置文件进行配置

```java
package cn.peiyang.spring.collectionType;

import lombok.Setter;

import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.Set;

@Setter
public class Stud {

    //1 数组类型属性
    private String[] courses;

    //2 list集合类型属性
    private List<String> list;

    //3 map集合类型属性
    private Map<String,String> map;

    //4 set集合类型属性
    private Set<String> sets;

    public void StudTest(){
        System.out.println(Arrays.toString(courses));
        System.out.println(list);
        System.out.println(map);
        System.out.println(sets);
    }

}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--1 集合类型属性注入-->
    <bean id="stud" class="cn.peiyang.spring.collectionType.Stud">
        <!--数组类型属性注入-->
        <property name="courses">
            <array>
                <value>java课程</value>
                <value>数据库课程</value>
            </array>
        </property>
        <!--list类型属性注入-->
        <property name="list">
            <list>
                <value>张三</value>
                <value>小三</value>
            </list>
        </property>
        <!--list类型属性注入-->
        <property name="map">
            <map>
                <entry key="JAVA" value="java"> </entry>
                <entry key="PHP" value="php"> </entry>
            </map>
        </property>
        <!--set类型属性注入-->
        <property name="sets">
            <set>
                <value>Mysql</value>
                <value>Redis</value>
            </set>
        </property>
    </bean>
</beans>

```



```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.collectionType.Stud;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class collectionTest {

    @Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

        Stud stud = context.getBean("stud",Stud.class);

        stud.StudTest();

    }
}

```



4、在集合里面设置对象类型值

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
package cn.peiyang.spring.collectionType;

import lombok.Setter;

import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.Set;

@Setter
public class Stud {

    //1 数组类型属性
    private String[] courses;

    //2 list集合类型属性
    private List<String> list;

    //3 map集合类型属性
    private Map<String,String> map;

    //4 set集合类型属性
    private Set<String> sets;

    //学生所学多门课程
    private List<Course> courseList;

    public void StudTest(){
        System.out.println(Arrays.toString(courses));
        System.out.println(list);
        System.out.println(map);
        System.out.println(sets);
        System.out.println(courseList);
    }

}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--1 集合类型属性注入-->
    <bean id="stud" class="cn.peiyang.spring.collectionType.Stud">
        <!--数组类型属性注入-->
        <property name="courses">
            <array>
                <value>java课程</value>
                <value>数据库课程</value>
            </array>
        </property>
        <!--list类型属性注入-->
        <property name="list">
            <list>
                <value>张三</value>
                <value>小三</value>
            </list>
        </property>
        <!--list类型属性注入-->
        <property name="map">
            <map>
                <entry key="JAVA" value="java"> </entry>
                <entry key="PHP" value="php"> </entry>
            </map>
        </property>
        <!--set类型属性注入-->
        <property name="sets">
            <set>
                <value>Mysql</value>
                <value>Redis</value>
            </set>
        </property>
        <!--注入list集合类型，值是对象-->
        <property name="courseList">
            <list>
                <ref bean="course1"></ref>
                <ref bean="course2"></ref>
            </list>
        </property>



    </bean>
    <!--创建多个course对象-->
    <bean id="course1" class="cn.peiyang.spring.collectionType.Course">
        <property name="cname" value="spring框架"> </property>
    </bean>
    <bean id="course2" class="cn.peiyang.spring.collectionType.Course">
        <property name="cname" value="MyBatis框架"> </property>
    </bean>


</beans>

```

```java
import cn.peiyang.spring.collectionType.Stud;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class collectionTest {

    @Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

        Stud stud = context.getBean("stud",Stud.class);

        stud.StudTest();

    }
}
```



5、把集合注入部分提取出来

（1）在spring配置文件中引入名称空间util

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--1 集合类型属性注入-->
    <bean id="stud" class="cn.peiyang.spring.collectionType.Stud">
        <!--数组类型属性注入-->
        <property name="courses">
            <array>
                <value>java课程</value>
                <value>数据库课程</value>
            </array>
        </property>
        <!--list类型属性注入-->
        <property name="list">
            <list>
                <value>张三</value>
                <value>小三</value>
            </list>
        </property>
        <!--list类型属性注入-->
        <property name="map">
            <map>
                <entry key="JAVA" value="java"> </entry>
                <entry key="PHP" value="php"> </entry>
            </map>
        </property>
        <!--set类型属性注入-->
        <property name="sets">
            <set>
                <value>Mysql</value>
                <value>Redis</value>
            </set>
        </property>
        <!--注入list集合类型，值是对象-->
        <property name="courseList">
            <list>
                <ref bean="course1"></ref>
                <ref bean="course2"></ref>
            </list>
        </property>



    </bean>
    <!--创建多个course对象-->
    <bean id="course1" class="cn.peiyang.spring.collectionType.Course">
        <property name="cname" value="spring框架"> </property>
    </bean>
    <bean id="course2" class="cn.peiyang.spring.collectionType.Course">
        <property name="cname" value="MyBatis框架"> </property>
    </bean>

</beans>

```

（2） 使用util标签完成list集合注入提取

```java
package cn.peiyang.spring.collectionType;

import lombok.Setter;

import java.util.List;

@Setter
public class Book {

    private List<String> list;

    public void testBook(){
        System.out.println(list);
    }

}
```

```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.collectionType.Stud;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import cn.peiyang.spring.collectionType.Book;

public class collectionTest {

    @Test
    public void test2(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");

        Book book = context.getBean("book", Book.class);

        book.testBook();
    }
}

```

