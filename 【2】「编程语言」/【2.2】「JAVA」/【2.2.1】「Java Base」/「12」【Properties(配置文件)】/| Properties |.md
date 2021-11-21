

# Properties（配置文件）

可持久化的映射类 继承HashTable 键和值都应该是字符串

生成的配置文件的后缀是.properties, 默认编码是西欧编码，中文转不了，按u16进行编码

配置文件可以随意改动



生成配置文件

```java
package cn.apix.properties;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.Properties;

public class PropertiesDemo {
    public static void main(String[] args) throws IOException {
        //创建对象
        Properties p = new Properties();

        //添加元素---键和值---字符串
        p.setProperty("name","lili");
        p.setProperty("gender","female");
        p.setProperty("age","10");

        //把要存储的内容传输到配置文件中---配置文件后缀必须是.properties
        //第二个参数---文件的内容解释
        p.store(new FileOutputStream("p.properties"),"xxxx");



    }
}

```



```java
package cn.apix.properties;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.util.Properties;

public class PropertiesDemo2 {
    public static void main(String[] args) throws IOException {

        //创建对象
        Properties p = new Properties();

        //加载指定配置文件
        p.load(new FileInputStream("p.properties"));

        //获取配置文件的内容
        //根据键获取值
        System.out.println(p.getProperty("name"));
        //根据键获取值
        //System.out.println(p.getProperty("name"，));

        //列出配置文件信息
        p.list(System.out);



    }
}

```







