## abstract(抽象)

所有子类对父类的某个方法都进行了不同程度的重写，父类的这个方法的方法体就没实际含义了，就可以把方法体去掉，用abstract修饰就变成了抽象方法，如果一个类中出现了抽象方法，这个类就要变成抽象类。抽象方法一定要背重写、如果一个普通类继承抽象类就要把所有的抽象方法都要进行重写，如果不想进行重写就可以把普通类变成抽象类





```java
package cn.tedu.abstractx;

public class abstractDemo {
    public static void main(String[] args) {
        Square s1=new Square(3);
        System.out.println(s1.getGirth());
    }
}

// 如果类中出现抽象方法，类要变成抽象类
abstract class Shape{
    private double x;
    private double y;

    public double getX() {
        return x;
    }

    public void setX(double x) {
        this.x = x;
    }

    public double getY() {
        return y;
    }

    public void setY(double y) {
        this.y = y;
    }

    public Shape(double x, double y){
        this.x=x;
        this.y=y;
    }

    //抽象方法
    //没有方法体
    //没有具体的方法体，只是提供了一个方法，让子类进行重写
    //抽象方法一定要被重写

    public abstract double getGirth();
    public abstract double getArea();

}

//如果一个普通类继承抽象类就要重写所有的抽象方法
//或者使一个普通类变成抽象类
class Rectangle extends Shape{
    public Rectangle(double x, double y) {
        super(x, y);
    }

    @Override
    public double getGirth() {
        return (this.getX() + this.getY())*2;
    }

    @Override
    public double getArea() {
        return this.getX() * this.getY();
    }
}

class Square extends Rectangle{
    public Square(double x) {
        super(x, x);
    }
}

class Circle extends Shape{
    public Circle(double r) {
        super(r,r);
    }

    @Override
    public double getGirth() {
        return 3.14*2* this.getX();
    }

    @Override
    public double getArea() {
        return Math.pow(this.getX(),2)*3.14;
    }
}



```



**abstract是关键字	修饰符--方法、类**

抽象方法可以重载

抽象方法不能被static/final/private分别修饰，不能进行重写

抽象类不一定含有抽象方法

抽象类无法调用对象



```java
package cn.tedu.abstractx;

public class abstractDemo {
    public static void main(String[] args) {
        Square s1=new Square(3);
        System.out.println(s1.getGirth());

        // s2对象是由匿名内部类产生的对象
        /*
        Shape s2=new Shape(1,2){
            @Override
            public double getArea() {
                return 0;
            }
        }//匿名内部类
         */

    }
}

// 如果类中出现抽象方法，类要变成抽象类
abstract class Shape{
    private double x;
    private double y;

    public double getX() {
        return x;
    }

    public void setX(double x) {
        this.x = x;
    }

    public double getY() {
        return y;
    }

    public void setY(double y) {
        this.y = y;
    }

    public Shape(double x, double y){
        this.x=x;
        this.y=y;
    }

    //抽象方法
    //没有方法体
    //没有具体的方法体，只是提供了一个方法，让子类进行重写
    //抽象方法一定要被重写

    public abstract double getGirth();
    public abstract double getArea();

}

//如果一个普通类继承抽象类就要重写所有的抽象方法
//或者使一个普通类变成抽象类
class Rectangle extends Shape{
    public Rectangle(double x, double y) {
        super(x, y);
    }

    @Override
    public double getGirth() {
        return (this.getX() + this.getY())*2;
    }

    @Override
    public double getArea() {
        return this.getX() * this.getY();
    }
}

class Square extends Rectangle{
    public Square(double x) {
        super(x, x);
    }
}

class Circle extends Shape{
    public Circle(double r) {
        super(r,r);
    }

    @Override
    public double getGirth() {
        return 3.14*2* this.getX();
    }

    @Override
    public double getArea() {
        return Math.pow(this.getX(),2)*3.14;
    }
}



```

