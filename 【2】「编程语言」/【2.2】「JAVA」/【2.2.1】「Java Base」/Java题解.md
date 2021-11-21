```java
/*
		读入三个整数，输出这三个整数中最大的一个。
*/
import java.util.Scanner;
public class maxInt{
  	public static void main(String[] args){
      	Scanner sc = new Scanner(System.in);
      	int num1 = sc.nextInt();
      	int num2 = sc.nextInt();
      	int num3 = sc.nextInt();
      	
      	int max = num1>num2?num1:num2;
      	max = max>num3?max:num3;
      	System.out.println(max);
    }
}
```





```java
/*
		读入一个三位数，计算其各位数字之和
*/
import java.util.Scanner;
public class numSum{
  	public static void main(String[] args){
      	Scanner sc = new Scanner(System.in);
      	int num = sc.nextInt();
      	int finalSum=0;
      	int j=100;
      	int s;
      	for(int i=0;i<3;i++){
          	s=num/j
            num = num%j;
            j=j/10;
            finalSum = finalSum+s;
        }
      	System.out.println(final);
    }
}
```







```java
/*
		读入一个小于10的整数n，输出它的阶乘n！
*/
import java.util.Scanner;
public class multipleN{
  	public static void main(String[] args){
      	Scanner sc=new Scanner(System.in);
      	
      	int n = sc.nextInt();
      	int finalSum=1;
      	for (int i=n; i>0; i--){
          	finalSum*=i;
        }
      	System.out.println(finalSum);
    }
}

```







```java
/*
		
*/
public class chickenNum{
  	public static void main(String[] args){
      	int x,y,z;
      	for(x=0;x<=33;x++){
          	for(y=0;y<=50;y++){
              	z=100-x-y;
              	if(z%3==0){
                  	if (4*x+2*y+z/3==100){
                      	System.out.println(x);
                      	System.out.println(y);
                      	System.out.println(z);
                    }
                  	
                }
            }
        }
    }
}
```







```java
/*
	打印9*9乘法表
*/
public class multipleTable{
  public static void main(String[] args){
    for(int i=1;i<10;i++){
      for(int j=1; j<=i;j++){
        System.out.print(i+"*"+j+"="+(i*j)+"\n");
      }
      System.out.println("\t");
    }
  }
}


```







```java
/*
	计算器
*/
import java.util.Scanner;
public class computor{
  public static void main(String[] args){
    Scanner sc=new Scanner(System.in);
    System.out.println("Please type in which compute method:(+-*/)")
    String m = sc.nextLine();
    
    System.out.println("Please type in the first number:");
    int num1 = sc.nextInt();
    System.out.println("Please type in the second number:");
    int num2 = sc.nextInt();
    
    if(m == "+"){
      System.out.println(num1+"+"+num2+"="+(num1+num2));
    }else if(m=="-"){
      System.out.println(num1+"-"+num2+"="+(num1-num2));
    }else if(m=="*"){
      System.out.println(num1+"*"+num2+"="+(num1*num2));
    }else if(m=="/"){
      System.out.println(num1+"/"+num2+"="+(num1/num2));
    }
    
  }
}


```







```java
/*
	36砖36人搬
*/

public class banZhuan{
  public static void main(String[] args){
    int x,y,z;
    for(x=0;x<=9;x++){
      for(y=0;y<=12;y++){
        z=36-x-y;
        if(z%2==0){
          if(4*x+3*y+z/2 == 36){
            System.out.println(x+","+y+","+z);
          }
        }
      }
    }
  }
}


```







```java
/*
	找出四位整数abcd满足(ab+cd)(ab+cd)=abcd
*/
public class siWeiZhengShu{
  public static void main(String[] args){
    for(int i=1000;i<=9999;i++){
      int num1 = Integer.valueOf(i.substring(0,2));
      int num2 = Integer.valueOf(i.substring(2,4));
      if((num1+num2)*(num1+num2) == i){
        System.out.println(i);
      }
    }
  }
}
```





```java
/*

*/
import java.util.Scanner;
public class nStar{
  public static void main(String[] args){
    Scanner sc=new Scanner(System.in);
    int n=sc.nextInt();
    for(int i=1;i<=n;i++){
      for(int j=1;j<=2*i-1;j++){
        System.out.print('*');
      }
     	System.out.println();
    }
  }
}


import java.util.Scanner;
public class nStar2{
  public static void main(String[] args){
    Scanner sc=new Scanner(System.in);
    int n=sc.nextInt();
    for(int i=1;i<=n;i++){
      for(int j=1;j<=2*n-1;j++){
        if(j >n-i && j <n+i){
          System.out.print('*');
        }else{
          System.out.print(' ');
        }
      }
     	System.out.println();
    }
  }
}



```



```java
//写一个函数add，接收两个整数作为参数，返回这两个整数的和。
public class Demo1{
  public static void main()
}


```



```java
//写一个函数接收一个整数，打印这个整数的所有因子
```



```java
//写一个函数，接收一个整数n，输出1+2+3+...+n的和
```



```java
//写一个函数，接收一个正整数，输出这个正整数是一个几位数
```











