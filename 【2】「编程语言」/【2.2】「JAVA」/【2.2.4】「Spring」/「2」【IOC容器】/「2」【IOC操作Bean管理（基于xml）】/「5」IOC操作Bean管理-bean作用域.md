## IOC操作Bean管理（bean作用域）

1. 在spring里面，设置创建bean实例是单实例还是多实例

   (1) 在spring配置文件bean标签里面有属性（scope）用于设置单实例还是多实例

   （2）scope属性值

   ​		第一个值 默认值， singleton，表示单实例对象

   ​		第二个值 prototype，表示多实例对象

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">

    <!--1  提取list集合类型属性注入-->
    <util:list id="bookList">
        <value>易筋经 </value>
        <value>九阴神功 </value>
        <value>九阳神功 </value>
    </util:list>


    <bean id="book" class="cn.peiyang.spring.collectionType.Book" scope="prototype">
        <property name="list" ref="bookList">

        </property>
    </bean>
</beans>

```

​			(3) singleton 和 prototype区别

​				设置scope值是singleton的时候，加载spring配置文件的时候就会创建单实例对象

​				设置scope值是prototype的时候，不是在加载spring配置文件的时候创建对象，而是在调用getBean方法的时候创建多实例对象

2. 在spring里面，默认情况下，bean是单实例对象

