## 基于注解实现属性注入

（1）@AutoWired：根据属性类型进行自动装配

​		第一步：service和dao对象创建，在service和dao类添加创建注解对象

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="cn.peiyang.spring">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Service"/>
    </context:component-scan>
</beans>

```



```java
package cn.peiyang.spring.dao;

public interface UserDao {
    public void add();
}

```



```java
package cn.peiyang.spring.dao;


import org.springframework.stereotype.Repository;

@Repository
public class UserDaoImpl implements UserDao{

    @Override
    public void add() {
        System.out.println("ao add....");
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
        ApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");

        UserService userService = context.getBean("userService", UserService.class);
        System.out.println(userService);
        userService.add();
    }
}
```





```java
package cn.peiyang.spring.service;


import cn.peiyang.spring.dao.UserDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;


//在注解里面value属性值可以省略不写
//默认值是类名称，首字母小写
//UserService -- userService

@Service  //<bean id="userService" class=""/>
public class UserService {

    //定义dao属性
    //不需要添加set
    //添加注入属性注解
    @Autowired  //根据类型进行注入
    //@Qualifier(value = "userDaoImpl2")
    private UserDao userDao;

    public void add(){
        System.out.println("service add.....");
        userDao.add();
    }
}
```







（2）@Qualifier：根据属性名称进行注入

（3）@Resource：可以根据类型注入也可以根据名称注入



```java
package cn.peiyang.spring.dao;


import org.springframework.stereotype.Repository;

@Repository(value="userDaoImpl2")
public class UserDaoImpl implements UserDao{

    @Override
    public void add() {
        System.out.println("ao add....");
    }
}

```

```java
package cn.peiyang.spring.service;


import cn.peiyang.spring.dao.UserDao;
import org.springframework.stereotype.Service;
//import javax.annotation.Resource;

//在注解里面value属性值可以省略不写
//默认值是类名称，首字母小写
//UserService -- userService

@Service  //<bean id="userService" class=""/>
public class UserService {

    //定义dao属性
    //不需要添加set
    //添加注入属性注解
    //@Autowired  //根据类型进行注入
    //@Qualifier(value = "userDaoImpl2")
    //private UserDao userDao;

    //@Resource(name = userDaoImpl2)
    private UserDao userDao;

    public void add(){
        System.out.println("service add.....");
        userDao.add();
    }
}

```



（4）@Value：注入普通类型属性

```java
package cn.peiyang.spring.service;


import cn.peiyang.spring.dao.UserDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
//import javax.annotation.Resource;

//在注解里面value属性值可以省略不写
//默认值是类名称，首字母小写
//UserService -- userService

@Service  //<bean id="userService" class=""/>
public class UserService {

    @Value(value="abc")
    private String oname;

    //定义dao属性
    //不需要添加set
    //添加注入属性注解
    //@Autowired  //根据类型进行注入
    //@Qualifier(value = "userDaoImpl2")
    //private UserDao userDao;

    //@Resource(name = userDaoImpl2)
    private UserDao userDao;

    public void add(){
        System.out.println("service add....."+oname);
        userDao.add();
    }
}

```

第二步 在servic注入dao对象，对象service这个类添加dao类型属性，在属性上面使用注解

```java
package cn.peiyang.spring.service;


import cn.peiyang.spring.dao.UserDao;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
//import javax.annotation.Resource;

//在注解里面value属性值可以省略不写
//默认值是类名称，首字母小写
//UserService -- userService

@Service  //<bean id="userService" class=""/>
public class UserService {

    @Value(value="abc")
    private String oname;

    //定义dao属性
    //不需要添加set
    //添加注入属性注解
    //@Autowired  //根据类型进行注入
    //@Qualifier(value = "userDaoImpl2")
    //private UserDao userDao;

    //@Resource(name = userDaoImpl2)
    private UserDao userDao;

    public void add(){
        System.out.println("service add....."+oname);
        userDao.add();
    }
}
```

