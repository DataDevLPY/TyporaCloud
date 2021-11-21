

## Static

![截屏2021-05-19 下午3.29.44](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-05-19 下午3.29.44.png)



![截屏2021-05-19 下午5.19.21](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-05-19 下午5.19.21.png)

![截屏2021-05-19 下午5.27.32](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-05-19 下午5.27.32.png)

* 静态方法可以重载

  —— 在一个class中方法被重载

* 静态方法不可以被重写

  —— 在子类方法中不可以被重写

```java
package cn.tedu.staticLearn;

public class staticDemo {
    int i=10;
    static int j=0;

    public static void main(String[] args) {

        Person p1 = new Person();
        p1.name = "Peiyang";
        p1.gender = '男';
        p1.age = 10;

        Person p2 = new Person();
        p2.name = "Xuan";
        p2.gender = '女';
        p2.age = 8;


        Person p3 = new Person();
        p3.name = "yuan";
        p3.gender = '中';
        p3.age = 120;


        System.out.println(p1.toString());
        System.out.println(p2.toString());
        System.out.println(p3.toString());

        //调用非静态方法(间接)
        new staticDemo().m();
        //静态方法不能直接调用非静态属性、变量
        //System.out.println(i);
        //静态方法不能直接调用非静态方法
        //m();


    }
    //静态方法
    public static void n(){
        System.out.println();
    }


    //非静态方法
    public void m(){
        //直接调用静态属性、变量
        System.out.println(j);
        //直接调用静态方法
        n();
    }
}

class Person{
    String name;
    static char gender;
    int age;

    public String toString(){
        return this.name + ',' + this.gender + ',' + this.age;
    }

}


```



添加@Override就可以编译出到底有没有重写



![截屏2021-05-20 上午11.12.28](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-05-20 上午11.12.28.png)

![截屏2021-05-20 上午11.34.49](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-05-20 上午11.34.49.png)

```java
package cn.tedu.staticLearn;

public class staticText1 {
    public static void main(String[] args) {
        new SB();
    }
}

class SA{

    static SD sd=new SD(); //属性
    static int i=10; //属性


    //静态代码块
    static {
        System.out.println("SA——静态代码块");
    }

    //构造代码块
    {
        System.out.println("SA——构造代码块");
    }

    //构造方法
    public SA(){
        System.out.println("SA——构造方法");
    }

}

class SB extends SA{
    //静态代码块
    //父类静态信息走完，走子类静态信息
    static {
        System.out.println("SB——静态代码块");
    }

    //父类对象级别走完，走子类对象级别
    //构造代码块
    {
        System.out.println("SB——构造代码块");
    }

    //构造方法
    public SB(){
        System.out.println("SB——构造方法");
    }
}

class SC{
    public SC(){
        System.out.println("SC-构造方法");
    }
}


class SD extends SC{

    public SD(){
        System.out.println("SD-构造方法");
    }
}
```

