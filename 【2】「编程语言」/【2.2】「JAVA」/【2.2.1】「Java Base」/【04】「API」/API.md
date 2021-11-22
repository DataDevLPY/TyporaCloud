##  API---Application Programming Interfaces 应用程序接口



提供了一系列的接口以及子接口或者实现类





### Object

是java中所有类的顶级父类，没有父类的类，所有类默认继承Object，所有类可以拿到Object中的属性和方法（重写）



重要方法

​	clone()

​			----返回的是存储了原对象信息的新对象，如果对象想要操作克隆方法就要让类实现Cloneable接口才能支持克隆，Cloneable中没有信息只是标记，实现这个接口就能执行克隆



​	finalize()

​			当垃圾收集确定没有对对象的引用时，由对象上的垃圾收集器调用。

​			无用对象调用这个方法通知系统进行垃圾回收

​			System.gc()

​	getClass()

​			返回的是实际创建类的类型

​	hashcode()

​			返回对象的hash值（不同对象hash一般不一样）

​			取值范围广

​			分布均匀

​	toString()

​			返回对象的字符表示形式

​			返回的是对象拼接的地址

​			重写toString用于输出属性值

​	equals()

​			Object里的方法是判断两个对象地址值是否相等

​			对equals方法可以进行重写

​					1.判断对象的地址值是否相等

​					2.判断参数是否为null

​					3.判断对象类型是否一致

​					4.比较属性（引用类型属性比较特殊）

​			能手写重写方法

```java
package cn.apix.object;

import java.util.Objects;

public class objectDemo4 {
    public static void main(String[] args) {

        Person p1=new Person();
        p1.name="Peiyang";
        p1.age=23;
        p1.gender="male";

        Person p2=new Person();
        //p2.name="Peiyang";
        p2.name=new String("Peiyang");
        p2.age=23;
        p2.gender="male";

        //比较：两个对象是否相等---判断地址值是否相等
        System.out.println(p1.equals(p2));
    }
}


class Person{
    String name;
    int age;
    String gender;

    //重写equals方法


    @Override
    public boolean equals(Object obj){
        // 1.比较对象地址值是否相等
        if(this==obj){//this代表p1对象
            return true;
        }
        // 2.判断参数是否为null
        if(this==null){
            return false;
        }
        // 3.判断两个对象的类型是否一致
        if(this.getClass()==obj.getClass()){
            return true;
        }

        //保证参数对象类型和Person类型一致，才能调用属性比值
        //向下造型
        Person p=(Person)obj;

        // 4. 比较属性是否相等
        if(this.age!=p.age){
            return false;
        }
        if(this.gender!=p.gender){
            return false;
        }
        //this.name是String对象调用的equals方法是String的
        //这个方法首先比较对象是否一致，如果不一致再比较内容是否一致
        // this.name==p.name----this.name=null p.name=null
        if(this.name==p.name || this.name != null&&this.name.equals(p.name)) {//比较地址值
            return true;
        }

        return false;
    }
}

```



### 练习

定义学生类，属性有姓名，年龄，性别，学号

​	重写equals方法

​	



​		

