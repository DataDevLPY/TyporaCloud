## AOP操作



1. Spring框架一般是基于AspectJ实现AOP操作

（1）AspectJ

* AspectJ不是Spring组成部分，独立AOP框架，一般把AspectJ和Spring框架一起使用，进行AOP操作



2. 基于AspectJ实现AOP操作

（1）基于xml配置文件实现

（2）基于注解方式实现（使用）



3. 在项目工程里面引入AOP相关依赖



4. 切入点表达式

（1）切入点表达式作用：知道对哪个类里面的哪个方法进行增强

（2）语法结构

execution([权限修饰符] [返回类型] [类全路径] [方法名称] ([参数列表]))



举例1:对cn.peiyang.spring.dao.BookDao类里面的add进行增强

```java
execution(* cn.peiyang.spring.dao.BookDao.add(..))
```

举例2:对cn.peiyang.spring.dao.BookDao类里面所有方法进行增强

```java
execution(* cn.peiyang.spring.dao.BookDao.*(..))
```

举例3:对cn.peiyang.spring.dao包里面所有类，类里面所有方法进行增强

```java
execution(* cn.peiyang.spring.dao.*.*(..))
```



