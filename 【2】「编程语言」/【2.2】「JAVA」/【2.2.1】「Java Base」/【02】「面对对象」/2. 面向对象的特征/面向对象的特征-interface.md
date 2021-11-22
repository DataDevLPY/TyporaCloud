## interface接口

一个抽象类中所有的方法都是抽象方法就可以转成接口，用interface来表示，一个普通类可以通过implements让类与接口之间产生关联关系---实现，支持多实现---一个类可以实现多个接口，类实现了接口之后就要重写所有的抽象方法，如果不想重写抽象方法就可以把类变成抽象类，接口与接口之间是多继承---接口可以同时继承多个接口，接口不是类

接口中不能创建对象，没有构造方法

接口中可以定义属性 属性被public final stati默认修饰

抽象方法默认被public abstract修饰

```java
package cn.tedu.abstractx;

public class abstractDemo2 {
}

//interface---接口---不是类
//接口与接口之间通过extends产生继承关系
//接口与接口之间是多继承
//interface Shape1 extends Object, Cloneable{
interface Shape2{
  	//默认被public final static修饰
    int i =1;
    //抽象方法默认被public abstract修饰
    void m();
    public abstract double getGirth();
    public abstract double getArea();
}

//implements --- 让类与接口产生关联关系
//实现的方式是多实现---类可以实现多个接口
//一个普通类实现一个接口就要重写所有的抽象方法
//如果不想重写所有的抽象方法就可以变成抽象类
//abstract class Rectangle2 implements Shape2, Cloneable{
class Rectangle2 implements Shape2{
    @Override
    public double getArea() {
        return 0;
    }

    @Override
    public double getGirth() {
        return 0;
    }
}



```



接口可以接受所有类型的值的转换



接口中不一定都是抽象方法

```java
package cn.tedu.interfacex;

public class interfaceDemo3 {
    public static void main(String[] args) {
        //向上造型
        B b=new C();
        //编译运行没错
        //在编译时期，针对两个对象的声明类是否具有继承关系，如果有继承关系就通过
        //c的声明类是C类，b的声明类是B类，B类和C类有继承关系，所以编译通过
        //在运行时期，针对两个对象的实际创建类是否一致，如果一致才能运行通过
        //c和b的实际创建类都是C
        //向下造型-----有向上才能有向下
        C c = (C)b;
        //ClassCastException---类型转换出错
        //编译没错运行有错
        //d的声明类是D，b的声明类也是D，两个之间有继承关系，所以编译通过
        //d的实际创建类是D，b的实际创建类是C，所以运行报错
        //D d = (D)b;

        // d的声明类是D，c的声明类是C，B和D之间没有继承关系，所以编译不通过
        //d = (D)c;

        //类与类之间是树状结构，能快速检测出两个类之间的关系,在编译和运行都加了检测
        //类与接口之间是多实现，是一个网状结构，不能快速检测两个类型之间的关系
        //在编译时期就不检测了，在运行期间j检测类型是否存在实现关系
        A a = (A)b;
        A a1 = (A)c;

        System.out.println("over...");

    }
}

interface A{}

class B{}

class C extends B implements A{} //既可以实现也可以继承

class D extends B{}
```



可以在接口定义集体方法（default，static）

通过lambda表达式对接口中的抽象方法进行重写，保证接口中只有一个抽象方法

一个接口中只有一个抽象方法-----函数式接口

```java
package cn.tedu.interfacex;

public class interfaceDemo4 {
    public static void main(String[] args) {
        //lambda表达式
        //(参数列表)->{重写方法的方法体}
        Calculator c=(int m, int n) -> {return m>n?m:n;};

        //如果方法只有一行代码就可以省略return以及{}
        Calculator c1=(int x, int y) -> x>y?x:y;

        //有接口中的抽象方法可以推导出参数类型，在此时可以不要类型表示
        Calculator c2=(x,y)->x>y?x:y;

        System.out.println(c.max(4,7));
        System.out.println(c1.max(4,7));
        System.out.println(c2.max(4,7));

    }
}

//接口---计算器
//如果想要使用lambda表达式需要保证接口中只有一个抽象方法
//函数式接口----只有一个抽象方法
@FunctionalInterface
interface Calculator{
    //求两个数的最大值
    //抽象方法
    int max(int x, int y);
    //求和
    public default int sum(int x, int y){
        return x+y;
    }

    //求乘积
    public static int multiple(int x, int y){
        return x*y;
    }
}
```



接口----优点

	1. 约束、模版
 	2. 向上造型的统一

```java
package cn.tedu.interfacex;
import java.util.Arrays;

public class interfaceDemo5 {
    public static void main(String[] args) {

        int[] arr = {3,7,4,6,2,9};
        Arrayx a = (int[] arr1)->{Arrays.sort(arr1);};
        Arrayx a1 = (arr1)->Arrays.sort(arr1);
        //::传递静态方法，只有一个参数
        Arrayx a2 = Arrays::sort;

        //对数组进行排序
        a.sort(arr);
        System.out.println(Arrays.toString(arr));
    }
}

//给数组进行排序
interface Arrayx{
    //抽象方法
    void sort(int[] arr);
}


```

