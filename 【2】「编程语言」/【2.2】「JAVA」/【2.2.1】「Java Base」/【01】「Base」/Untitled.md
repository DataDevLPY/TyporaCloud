##  基本编译

**通过javac命令进行编译：**

```javac Demo.java```

**通过java命令运行：**

```Java Demo```

class Demo{

​	public static void main(String[] args){ }

}

**提取文档：**

javadoc -d mydoc person.java



## 标识符



## 变量

 **数据类型**

整数型：byte,   -128-127

​				short,

​				int,

​				long

浮点型：float, double

字符型：char

布尔型：boolean



**类型转换**

强制类型转换

自动类型转换



## 运算符

算术运算符

赋值运算符

比较（关系）运算符

逻辑运算符

位运算符 a^b^b=a

三目（元）运算符   a>b?a:b;





## 流程控制语句

Import java.ytil.Scanner;

Scanner sc =new Scanner(System.in);

String s=sc.nextLine();



**顺序结构**



**选择结构**

 if(){

;}else if{

;}else{

;}

```java
import java.util.Scanner;
public class ifDemo2{
        public static void main(String[] args){
                Scanner sc=new Scanner(System.in);
                int score=sc.nextInt();

                if(score>=0 && score<=10){
                        System.out.println("黑铁");
                }else if(score>10&&score<=20){
                        System.out.println("青铜");
                }
        }
}

```



```java
Switch(num){

Case 值:

​	_;

​	break;

case 值:

​	-;

​	break;

}
```



**循环结构**

for(初始值；判断条件；控制条件){

​	循环体；

}



while(){

​	————；

}