# Junit (单元测试)

导入测试库



可以支持单元测试的（三无）方法 --- 没有static、没有返回值、没有参数

```java
package cn.apix.Junit;

import org.junit.Test;

public class JunitDemo {


    //加上单元测试 --- 基于main方法来执行
    @Test
    public void m(){
        System.out.println(2);
    }

    //空白处右击所有的单元测试都执行
    @Test
    public void n(){//形参---用于就接受传递的实参
        System.out.println(3);
    }
}

```

