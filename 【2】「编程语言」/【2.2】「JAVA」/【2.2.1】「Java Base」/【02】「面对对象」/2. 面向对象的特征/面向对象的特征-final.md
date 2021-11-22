## final(最终)

关键字---修饰符	数据、方法、类



**修饰数据（最终值）**

​		如果修饰的是基本是类型数据值不能改变

​		如果修饰的是引用数据类型地址值不能改变，元素值可以改变

​		如果成员变量被final修饰且没有给值，要保证在创建对象之前给值

​		如果成员变量被static和final共同修饰（静态常量），要保证在类加载完成之前给值



```java
package cn.tedu.finalLearn;

public class finalDemo1 {
    public static void main(String[] args) {
        //final修饰基本类型数据值不能改变
        final int i=1;
        //final修饰的饮用类型数据地址值不能改变，元素值可以改变
        final int[] arr={1,2,3};
        //数组元素可以改变
        arr[0]=10;
        //地址值不能改变
        //arr = new int[6];

        //数组长度是最终值
        //arr.length = 10;
    }
}
```



```java
package cn.tedu.finalLearn;

public class finalDemo2 {
    public static void main(String[] args) {

    }

}

class FDemo{
    //属性
    final int i;

    //构造代码块--保证在创建对象之前给值
    {
        //i=2;
    }

    //保证所有的构造方法都要有初始化
    public FDemo(){
        i=4;
    }

    public FDemo(int age){
        i=2;
    }
}


class FDemoA{
    //属性
    static final int i; //静态常量

    //构造代码块--保证在创建对象之前给值
    //静态代码块
    static{
        i=2;
    }

    //保证所有的构造方法都要有初始化
    public FDemoA(){
        //i=4;
    }

    public FDemoA(int age){
       // i=2;
    }
}
```





**修饰方法（最终方法）**

​		可以重载

​		不可以重写



**修饰类（最终类）**

​		final修饰的类不能继承，可以有父类









