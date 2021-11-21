## AOP底层原理



1. AOP底层使用动态代理

（1）有两种情况动态代理

​		第一种 有接口情况，使用JDK动态代理

​		-- 创建接口实现类代理对象，增强类的方法

​			![image-20210708142841691](/Users/peiyang/Library/Application Support/typora-user-images/image-20210708142841691.png)

​		第二种 没有接口情况，使用CGLIB动态代理

​		-- 创建子类的代理对象，增强类的方法 

![image-20210708142854371](/Users/peiyang/Library/Application Support/typora-user-images/image-20210708142854371.png)



2. JDK动态代理，使用Proxy类里面的方法

（1）调用newProxyInstance方法

​	三个参数：

​		ClassLoader，类加载器

​		类<?>[ ] interfaces 增强方法所在的类，这个类实现的接口，支持多个接口

​		InvocationHandler	实现这个接口，创建代理对象，写增强的方法

3. 编写JDK动态代理代码

（1）创建接口，定义方法

```java
package cn.peiyang.spring;

public interface UserDao {
    
    public int add(int a, int b);
    
    public String update(String id);
}
```



（2）创建接口实现类，实现方法

```java
package cn.peiyang.spring;

public class UserDaoImpl implements UserDao{

    @Override
    public int add(int a, int b) {
        return a+b;
    }

    @Override
    public String update(String id) {
        return id;
    }
}

```



（3）使用Proxy

```java
package cn.peiyang.spring;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;

public class JDKProxy {
    public static void main(String[] args) {
        //创建接口实现类代理对象
        Class[] interfaces = {UserDao.class};

        UserDaoImpl userDao = new UserDaoImpl();

        UserDao dao = (UserDao) Proxy.newProxyInstance(JDKProxy.class.getClassLoader(),interfaces, new UserDaoProxy(userDao));

        String result = dao.update("55");

        System.out.println(result);
    }
}

//创建代理对象代码
class UserDaoProxy implements InvocationHandler {

    //把创建的是谁的代理对象，把谁传递过来
    //有参构造传递

    private Object obj;
    public UserDaoProxy(Object obj){
        this.obj=obj;
    }


    //增强的逻辑
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        //方法之前
        System.out.println("方法之前执行。。。"+method.getName()+": 传递的参数是。。。"+ Arrays.toString(args));


        //被增强的方法执行
        Object res = method.invoke(obj,args);


        //方法之后
        System.out.println("方法之后执行。。。"+obj);


        return res;
    }
}

```



















