## String()

​				是一个最终类，没有子类，所有字符串的常量都是String的对象

​				String创建之后不能改变，底层是由不可改变的字符数组实现的 

​				如果常量池中已有字符串常量，后续出现的字符串常量重复了就用前面的---共享

​				所有的常量都要存储在常量池，其他类型的常量直接存储的是常量值，字符串常量存储的指向字符数组的地址值



  ![截屏2021-06-08 上午10.07.18](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-08 上午10.07.18.png)



**比较+和StringBuilder的效率**

1. 空间（在空间的维度上，StringBuilder会更好）

   1).用+

```java
String[] ss={.......};
String str="";
for(int i=0;i<ss.length;i++){
  str+=ss[i];
  //new StringBuilder(str).append(ss[i]).toString();
}
```

​		2).用StringBuilder()

```java
StringBuilder sb=new StringBuilder();
for(int i=0;i<ss.length;i++){
  sb.append(ss[i])
}
String str = sb.toString();
```



2. 时间（从时间维度上，StringBuilder更优）

 





StringBuilder线程不安全，但是效率高

StringBuffer线程安全，但是效率低





**重要方法**

1. charAt(). ---  根据字符串下标返回指定字符
2. length() --- 返回字符串长度
3. toCharArray() ---  把一个字符串转换为一个字符数组
4. new String(char[] cs) --- 可以将一个字符数组转成一个新的字符串
5. compareTo() --- 返回两个字符串对象字典排序的差值，如果差值一直为0直到一个字符串遍历结束返回的就是长度的差值
6. concat()---把新字符拼接到原字符串的结尾生成一个新字符串在返回
7. contains() --- 判断新串是否是原串的子串
8. startsWith()
9. EndsWith()
10. instanceOf --- 前面是对象，后面是类型 
11. equals() --- String重写的方法首先判断他们的地址值是否一致， 如果不一致则判断他们的内容是否一致
12. equalsIgnoreCase()



```java
package cn.apix.string;

import java.util.Arrays;

public class stringDemo3 {
    public static void main(String[] args) {
        //创建字符串对象
        String str="peiyang";
        //给定字符串下标，返回一个字符
        System.out.println(str.charAt(3));

        //遍历字符串内容
        //length()---字符串的长度
        System.out.println("charAt()用法：");
        for(int i=0;i<str.length();i++){
            System.out.println(str.charAt(i));
        }

        System.out.println("toCharArray()用法：");
        //根据字符串对象返回一个新的字符串数组
        char[] cs = str.toCharArray();
        System.out.println(Arrays.toString(cs));

        //字符数组转换为字符串
        //String s = new String(cs);
        String s = new String(cs,0,3);
        System.out.println(s);

    }
}

```







### 编码

getBytes() ----可以根据指定的码表返回一个字节数组或者不指定码表，就按照默认系统平台码来进行编码返回一个字节数组

new String(bs[],offset,int length) ---- 

Hashcode() ----- 返回的是字符串对象的哈希码值

indexOf() ---- 返回指定新串在原串第一次出现的下标值，如果没有出现就返回-1

lastIndexOf() --- 

intern() ---- 把字符串对象的引用转向到常量池

isEmpty() --- 判断是否是空串

		* ""代表内存有空间但是没有元素--空
		* null代表内存空间没有开辟

replace() --- 把原串中所有的指定字符替换成新字符返回一个新字符串

substring() --- 给定起始下标，从下标开始往后截取成一个新字符串进行返回

toUpperCase() 

toLowerCase()

trim() --- 去除字符串前后空格

valueOf() --- 静态方法，可以把所有的数据类型的数据转成新字符串返回









把文字转成具体的数字		码表：转换规则



西欧码表（ISO8859-1  1个字节）

GB2312（简体中文  2个字节）

BID5（繁体中文  2个字节）

国标码（GBK  2个字节）

Unicode（u8（3个字节），u16（2个字节））



所有的完整码表都默认兼容西欧码表



