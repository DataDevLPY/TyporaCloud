## IOC操作Bean管理（xml注入其他类型属性）

1.字面量

（1）null值

 ```xml
<property name="address">
  <null/>
</property>
 ```

（2）属性值包含特殊符号

​		把<>进行转义 &lt ;  &gt ;

​		把带特殊符号内容写到CDATA

```xml
<property name="address">
  <value><![CDATA[<<南京>>]]></value>
</property>
```



2. 注入属性-外部bean

（1）创建两个类service类和dao类

（2）在service调用dao里面的方法

方法一：

```java
package cn.peiyang.spring.dao;

public class UserDaoImpl implements UserDao {

    @Override
    public void update() {
        System.out.println("service add........");
        //创建UserDao对象
        UserDao userDao = new UserDaoImpl();
        userDao.update();
    }
}
```

```java
package cn.peiyang.spring.dao;

public interface UserDao {

    public void update();
}
```



方法二：（spring）

```java
package cn.peiyang.spring.dao;

public interface UserDao {

    public void update();
}
```

```java
package cn.peiyang.spring.dao;

public class UserDaoImpl implements UserDao {

    @Override
    public void update() {
        System.out.println("dao update........");
        //创建UserDao对象
        //UserDao userDao = new UserDaoImpl();
        //userDao.update();
    }
}
```

```java
package cn.peiyang.spring.service;

import cn.peiyang.spring.dao.UserDao;


public class UserService {

    private UserDao userDao;

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void add(){
        System.out.println("service add........");
        userDao.update();
    }

}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--service和Dao对象的创建-->
    <bean id="userService" class="cn.peiyang.spring.service.UserService">
        <!--注入userDao对象
            name属性值：类里面属性名称
            ref属性：创建userDao对象bean标签id值
        -->
        <property name="userDao" ref="userDaoImpl"> </property>
    </bean>
    <bean id="userDaoImpl" class="cn.peiyang.spring.dao.UserDaoImpl"> </bean>

</beans>

```

Test

```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.service.UserService;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestUserService {
    @Test
    public void test(){

        ApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");

        UserService userService = context.getBean("userService",UserService.class);

        userService.add();

    }
}

```



3. 注入属性-内部bean

--例子：一个部门对应多名员工，一个员工只对应一个部门

```java
//部门
package cn.peiyang.spring.bean;

public class Dept {

    private String dname;

    public void setDname(String dname) {
        this.dname = dname;
    }

    @Override
    public String toString() {
        return "Dept{" +
                "dname='" + dname + '\'' +
                '}';
    }
}
```

```java
//员工
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestOrder {

    @Test
    public void testOrder(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

        Orders order = context.getBean("order",Orders.class);

        System.out.println(order);
        order.orderTest();
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--内部bean-->
    <bean id="emp" class="cn.peiyang.spring.bean.Emp">
        <!--设置两个普通属性-->
        <property name="ename" value="lucy"> </property>
        <property name="gender" value="female"> </property>

        <!--设置对象类型属性-->
        <property name="dept">
            <bean id="dept" class="cn.peiyang.spring.bean.Dept">
                <property name="dname" value="安保部"> </property>
            </bean>
        </property>
    </bean>
</beans>

```

```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.bean.Emp;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestEmp {
    @Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean3.xml");
        Emp emp = context.getBean("emp", Emp.class);

        emp.add();
    }
}
```



4. 注入属性-级联赋值

```java
//部门
package cn.peiyang.spring.bean;

public class Dept {

    private String dname;

    public void setDname(String dname) {
        this.dname = dname;
    }

    @Override
    public String toString() {
        return "Dept{" +
                "dname='" + dname + '\'' +
                '}';
    }
}
```

```java
//员工
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestOrder {

    @Test
    public void testOrder(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");

        Orders order = context.getBean("order",Orders.class);

        System.out.println(order);
        order.orderTest();
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--级联赋值-->
    <bean id="emp" class="cn.peiyang.spring.bean.Emp">
        <!--设置两个普通属性-->
        <property name="ename" value="lucy"> </property>
        <property name="gender" value="female"> </property>

        <!--级联赋值-->
        <property name="dept" ref="dept"> </property>
      	<property name="dept.dname" value="安保部"> </property>
    </bean>
  	<bean id="dept" class="cn.peiyang.spring.bean.Dept"> </bean>
</beans>
```

```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.bean.Emp;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class TestEmp {
    @Test
    public void test(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean4.xml");
        Emp emp = context.getBean("emp", Emp.class);
        emp.add();
    }
}
```

