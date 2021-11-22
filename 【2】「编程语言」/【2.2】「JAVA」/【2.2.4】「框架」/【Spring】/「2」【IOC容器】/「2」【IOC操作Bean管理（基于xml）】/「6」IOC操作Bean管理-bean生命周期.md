## IOC操作Bean管理（bean生命周期）

1. 生命周期

（1） 从对象创建到对象销毁的过程



2. bean生命周期

（1）通过构造器创建bean实例（无参构造）

（2）为bean的属性设置值和对其他bean引用（调用set方法）

（3）调用bean的初始化的方法（需要进行配置）

（4）bean可以使用了（对象获取到了）

（5）当容器关闭的时候，调用bean的销毁的方法（需要进行配置销毁的方法）



3. 演示bean的生命周期



4. Bean的后置处理器，bean的生命周期有七步

（1）通过构造器创建bean实例（无参构造）

（2）为bean的属性设置值和对其他bean引用（调用set方法）

（3）把bean的实例传递bean后置处理器的方法

（4）调用bean的初始化的方法（需要进行配置）

（5）把bean实例传递bean后置处理器的方法

（6）bean可以使用了（对象获取到了）

（7）当容器关闭的时候，调用bean的销毁的方法（需要进行配置销毁的方法）



5 演示添加后置处理器效果

（1）创建类，实现接口BeanPostProcessor，创建后置处理器

```java
package cn.peiyang.spring.bean;


public class Orders {

    //无参构造
    public Orders() {
        System.out.println("第一步 执行无参构造创建bean实例");
    }

    private String oname;

    public void setOname(String oname) {
        this.oname = oname;
        System.out.println("第二部 调用set方法设置属性值");
    }

    //创建执行的初始化的方法
    public void initMethod(){
        System.out.println("第三步 执行初始化的方法");
    }


    //创建销毁方法
    public void destroyMethod(){
        System.out.println("第五步 执行销毁的方法");
    }
}
```



```java
package cn.peiyang.spring.bean;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

public class MyBeanPost implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {

        System.out.println("在初始化之前执行的方法");
        return BeanPostProcessor.super.postProcessBeforeInitialization(bean, beanName);
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {

        System.out.println("在初始化之后执行的方法");
        return BeanPostProcessor.super.postProcessAfterInitialization(bean, beanName);
    }
}
```



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="order" class="cn.peiyang.spring.bean.Orders" init-method="initMethod" destroy-method="destroyMethod">
        <property name="oname" value="手机"> </property>
    </bean>

    <!--配置后置处理器-->
    <bean id="myBeanPost" class="cn.peiyang.spring.bean.MyBeanPost"> </bean>
</beans>

```



```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.bean.Orders;
import cn.peiyang.spring.collectionType.Book;
import cn.peiyang.spring.collectionType.Course;
import cn.peiyang.spring.collectionType.Stud;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class collectionTest {

    @Test
    public void LifeTest(){
        ApplicationContext context = new ClassPathXmlApplicationContext("bean4.xml");

        Orders order = context.getBean("order",Orders.class);
        System.out.println("第四步 获取创建bean实例对象");
        System.out.println(order);

        //手动让bean实例销毁
        ((ClassPathXmlApplicationContext)context).close();
    }

}
```



