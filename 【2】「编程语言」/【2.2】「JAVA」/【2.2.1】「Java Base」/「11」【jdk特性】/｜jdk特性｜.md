# jdk特性



![截屏2021-06-26 下午8.59.44](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-26 下午8.59.44.png)

![截屏2021-06-26 下午9.00.12](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-26 下午9.00.12.png)











## 可变参数

```java
package cn.jdk5.vars;

public class VarsDemo {
    public static void main(String[] args) {
        System.out.println(m(2));
        System.out.println(m(1,2));
        //System.out.println(m());//没往里存储

        System.out.println(m(2.3,2));
    }

    //...可变参数---表示可以接受多个这样类型的参数
    //可变参数底层就是一个数组，参数的值依次存放到数组元素中
    //可变参数一定在最右边而且只有一个
    public static int m(double x, int... n){
        return n[0];
    }
}
```



## 枚举

一个一个列举出来

enum代表一个枚举类

枚举类中所有的枚举常量必须放在第一行

枚举类里可以定义属性和方法

构造方法一定要私有化

可以定义抽象方法

switch-case

```java
package cn.jdk5.Enum;

import java.util.Scanner;

public class EnumDemo {
    public static void main(String[] args) {
        //作为一个类还是得提供服务---外部获取到对象

        Season s= Season.spring;   //new Season();

        //枚举类型--匹配枚举类型中所有的枚举常量
        switch (s){
            case spring:
                System.out.println("春天");
                break;

            case summer:
                System.out.println("夏天");
                break;

            case autumn:
                System.out.println("秋天");
                break;

            case winter:
                System.out.println("冬天");
                break;

            default:
                break;
        }


    }
}

//枚举
enum Season{
    //枚举类的属性---枚举常量(对象)---要求一定要在首行
    //public final static Season spring = new Season();
  	spring{
        @Override
        public void n(){

        }
    },summer {
        @Override
        public void n() {

        }
    },autumn {
        @Override
        public void n() {

        }
    },winter {
        @Override
        public void n() {

        }
    };

    //普通属性和方法
    int i=1;
    public void m(){}

    //构造方法---私有化
    private Season(){}

    //抽象方法
    public abstract void n();

}



/*
//季节
class Season{
    //外部获取不到的构造方法---外部拿不到对象构建过程
    //构造方法私有化

    private Season(){

    }

    //本类可以访问私有化构造方法---创建本类对象
    //加上static---让外部不创建对象的前提下获取到这个属性
    //加上final --- 对象地址值不能改变
    //加上public --- 外界可以拿

    public final static Season spring = new Season();
    public final static Season summer = new Season();
    public final static Season autumn = new Season();
    public final static Season winter = new Season();



}
*/
```









