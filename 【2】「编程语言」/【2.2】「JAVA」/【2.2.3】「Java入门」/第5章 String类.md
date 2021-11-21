





# String 类

* 包含在“ ”中

## 5.2 创建字符串

```java
public class StringClass {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		char a[] = {'g','o','o','d'};
		String s = new String(a);
		System.out.println((String) s);
		//System.out.println((String) x);
	}
}
```

```java
public class StringClass {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		char a[] = {'g','o','o','d'};
		String s = new String(a,1,3);
		System.out.println((String) s);
		//System.out.println((String) x);
	}
}
```

```java
public class StringClass {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String str1,str2;
		str1 = "we are student";
		System.out.println((String) str1);
		//System.out.println((String) x);
	}
}
```

```java
public class Join {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String str1,str2;
		str1 = "we are student";
		str2 = "Hello,";
		String s = str2+" " + str1; 
		System.out.println((String) s);
		//System.out.println((String) x);
	}
}
```

"Hello, we are student"

## 5.3 获取字符串信息

### 5.3.1 字符串长度

```java
public class StringLength {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String str = "we are student";
		int size = str.length();
		System.out.println((int) size);
	}
```

### 5.3.2 字符串查找

```java
public class StringFind {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String str = "we are student";
		int size = str.indexOf("a");
		int last = str.lastIndexOf("e");
		System.out.println((int) size);
		System.out.println((int) last);
	}
}
```

```java
public class StringFind {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String str = "we are student";
		char mychar = str.charAt(5);
		System.out.println((char) mychar);
	}
}
```



## 5.4 字符串操作

```java
// 字符截取
str.substring(0，3)
// 去除前导空格和后导空格
str.trim()
// 替换
str.replace("a","A")
// 判断开始或结尾
boolean str.startWith("22");
boolean str.endWith("22");
//判断字符串是否相等
String tom = new String("I am a student");
String jerry = new String("I am a student");
boolean b = (tom == jerry);

str.equals(String otherstr)
str.equalsIgnoreCase(String otherstr)
  
//按字典顺序比较两个字符串
str.compareTo(String otherstr)

//字母大小写转换
str.toLowerCase()
str.toUpperCase()

//字符串分割
public class StringFind {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String str = "192.168.0.1";
		String[] firstArray = str.split("\\.",2);
		
		for(String a: firstArray) {
			System.out.print(a+"\n");
		}
	}
}
```



## 5.5 格式化字符串

**1. 日期格式化**

```java
public class Eval {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Date date = new Date();
		
		String year = String.format("%tY", date);
		String month = String.format("%tm", date);
		String day = String.format("%td", date);
		System.out.println(year+'\n'+month+'\n'+day+'\n');
		
	}

}
```





![8881613032812_.pic](/Users/peiyang/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/46270fd164b1a21483416e93c94fe200/Message/MessageTemp/7cce3e9bae82308adb5699063c565d68/Image/8881613032812_.pic.jpg)



![8891613032813_.pic](/Users/peiyang/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/46270fd164b1a21483416e93c94fe200/Message/MessageTemp/7cce3e9bae82308adb5699063c565d68/Image/8891613032813_.pic.jpg)

**2. 常规类型格式化**

```java
public class Eval {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Date date = new Date();
		
		String str = String.format("%d", 400/2);
		String str2 = String.format("%b", 3>5);
		String str3 = String.format("%x", 200);
		
		System.out.println(str+"\n"+str2+"\n"+str3);
	}

}
```



![8901613032814_.pic](/Users/peiyang/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/46270fd164b1a21483416e93c94fe200/Message/MessageTemp/7cce3e9bae82308adb5699063c565d68/Image/8901613032814_.pic.jpg)



![8911613032815_.pic](/Users/peiyang/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/46270fd164b1a21483416e93c94fe200/Message/MessageTemp/7cce3e9bae82308adb5699063c565d68/Image/8911613032815_.pic.jpg)



## 5.6 使用正则表达式

![5431613133300_.pic](/Users/peiyang/Library/Containers/com.tencent.xinWeChat/Data/Library/Application Support/com.tencent.xinWeChat/2.0b4.0.9/46270fd164b1a21483416e93c94fe200/Message/MessageTemp/770716673a126538c5fb1ca0e713e060/Image/5431613133300_.pic.jpg)



```java
public class Judge {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String regex = "\\w+@\\w+(\\.\\w{2,3})*\\.\\w{2,3}";
		String str1 = "aaa@";
		String str2 = "aaaaa";
		String str3 = "111@111ffyu.dfg.com";
		
		if (str1.matches(regex)) {
			System.out.println(str1);
		}
		
		if (str2.matches(regex)) {
			System.out.println(str2);
		}
		
		if (str3.matches(regex)) {
			System.out.println(str3);
		}
		
	}

}
```



## 5.7 字符串生成器

```java
public class Jerque {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		String str="";
		
		long starTime = System.currentTimeMillis();
		for (int i=0; i<10000; i++) {
			str = str + i;
		}
		long endTime = System.currentTimeMillis();
		long time = endTime-starTime;
		System.out.println((long) time);
		
		StringBuilder builder = new StringBuilder("");
		starTime = System.currentTimeMillis();
		for (int j=0; j<10000; j++) {
			builder.append(j);
		}
		
		endTime = System.currentTimeMillis();
		long time2 = endTime-starTime;
		System.out.println((long) time2);
		
	}

}
```



```java
append(content)
  
insert(int offset arg)
  
delete(int start, int end)
```





























