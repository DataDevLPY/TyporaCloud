# JVM

![截屏2021-07-02 下午5.31.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-02 下午6.14.46.png)



![截屏2021-07-02 下午6.12.50](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-07-02 下午6.12.50.png)



```java
package cn.jdk5.jvm;

public class JVMDemo {
    static int count = 0;

    public static void main(String[] args) {
        sum(1000000);
    }


    public static int sum(int n){
        System.out.println(count++);

        if(n==1){
            return 1;
        }

        return sum(n-1) + n;
    }

}
```

