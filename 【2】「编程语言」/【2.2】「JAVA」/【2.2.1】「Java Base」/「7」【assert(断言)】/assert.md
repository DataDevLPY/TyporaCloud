# assert 

对程序结果进行预测性的判断



需要手动开启-ea

![截屏2021-06-26 下午8.49.59](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220033263.png?token=AWS37JOEKBAXOG3YEYUAACDBTJ2Q6)



```java
package cn.assertx;

import java.util.Scanner;

public class AssertDemo {

    public static void main(String[] args) {
        //输入一个小于10的正数
        int num = new Scanner(System.in).nextInt();

        num >>= 1;
        //预测结果---如果预测正确代码接着执行，如果错误就报错
        //AssertionError
        //需要手动开启断言
        assert num<5:"预测结果要小于5";

        System.out.println(num);

    }

}
```

