## 完全注解开发



1. 创建配置类，替代xml配置文件

```java
package cn.peiyang.spring.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration //作为配置类，替代xml配置文件
@ComponentScan(basePackages = {"cn.peiyang.spring"})
public class SpringConfig {
    
}
```

2. 编写测试类

```java
package cn.peiyang.spring.testDemo;

import cn.peiyang.spring.config.SpringConfig;
import cn.peiyang.spring.service.UserService;
import org.junit.jupiter.api.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class collectionTest {

    @Test
    public void AllAnnotationTest(){
        ApplicationContext context = new AnnotationConfigApplicationContext(SpringConfig.class);

        UserService userService = context.getBean("userService", UserService.class);
        System.out.println(userService);
        userService.add();
    }

}
```