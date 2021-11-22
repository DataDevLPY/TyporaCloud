# IO流

本质就是数据传输的一套机制 

InPut，OutPut  Stream 输入输出流



根据数据传输的方向：往内存传输数据---输入流

​										内存往外传输数据---输出流

根据数据传输的方式：字符流、字节流



java IO流的四大基本流

![截屏2021-06-23 下午9.02.48](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220033491.png?token=AWS37JM2FYTP43XKU6IMJ73BTJ2TE)



四大基本流对应的类都是抽象类



数据存储/获取的位置：硬盘、内存、网络、外设设备





### 硬盘

**字符**

往硬盘上一个txt文件写入数据  字符输出流   文件  FileWriter

从一个硬盘的txt文件读取数据  字符输入流   文件  FileReader



![截屏2021-06-23 下午10.29.44](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220033765.png?token=AWS37JIQVX26H7B4DMUKSWLBTJ2TK)





```java
package cn.tedu.file.fileWriter;

import java.io.FileWriter;
import java.io.IOException;

public class FileWriterDemo1 {
    public static void main(String[] args) throws IOException {
        //创建对象
        //检查正确与否，如果没有此文件，就会创建空文件
        //如果文件已存在，就会创建空文件覆盖源文件
        //保证在读之前是个空文件
        FileWriter writer = new FileWriter("/Users/peiyang/Demo.txt");

        //写数据
        //底层基于缓冲区j进行数据传输
        //默认要求缓冲区数据装满才能进行传输
        //下面的数据没有装满，缓冲区就没有进行传输
        writer.write("Hello!");

        //冲刷缓冲区
        //不管缓冲区数据有没有存满，都要进行传输
        //防止数据滞留在缓冲区，容易丢失
        writer.flush();

        //关闭连接通道 ---- 关流
        //在关闭连接通道之前会自动进行冲刷
        writer.close();

        //强制对象值
        //把对象置为无用对象，在某个时间进行垃圾回收
        writer = null;
        

    }
}

```





### 异常处理

1. 在try块外进行生命对象，在try块里进行对象初始化
2. 保证对象已经进行正常的初始化才能进行关流操作
3. 不管关流成功与否，都要对对象进行置为无用对象的操作
4. 流关闭失败有可能是在自动冲刷缓冲区前，保证数据不能滞留在缓冲区，自己进行手动冲刷缓冲区



```java
package cn.tedu.file.fileWriter;

import java.io.FileWriter;
import java.io.IOException;

public class FileWriterDemo2 {
    public static void main(String[] args) {
        //创建对象
        //1.声明对象
        FileWriter writer = null ; //保证有值，但不能保证try块里一定能赋值
        try {
            //2.真实赋值
            writer = new FileWriter("/Users/peiyang/Demo.txt");

            //写出数据
            writer.write("123");
            //手动冲刷缓冲区
            writer.flush();
        } catch (IOException e) {
            e.printStackTrace();
        }finally{//不管try里代码正确与否

            //保证对象初始化成功有值才能正确调用关流方法
            if(writer != null){
                try {
                    writer.close(); //关流有可能失败
                } catch (IOException e) {
                    e.printStackTrace();
                }finally{
                    //对象置为无用对象--垃圾回收
                    writer=null;
                }
            }
        }
    }
}

```



```java
package cn.tedu.file.fileWriter;

import java.io.FileWriter;

public class FileWriterDemo3 {
    public static void main(String[] args) {

        //自动冲刷缓冲区、关流
        try(FileWriter writer = new FileWriter("/Users/peiyang")){
            writer.write("123");
        }catch(Exception e){

        }
    }
}
```



### Read

```java
package cn.tedu.file.fileReader;

import java.io.IOException;
import java.io.FileReader;

public class FileReaderDemo {
    public static void main(String[] args) throws IOException {
        FileReader reader = new FileReader("/Users/peiyang/Demo.txt");
        //返回的是读取到的字符的编码值
        //读取结束的标志就是返回-1
        //System.out.println((char)reader.read());

        int len = -1;
        while((len= reader.read())!= -1){
            System.out.println(len);
        }
    }
}

```



```java
package cn.tedu.file.fileReader;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.IOException;

public class FileReaderDemo2 {
    public static void main(String[] args) throws IOException {
        //创建对象
        FileReader reader = new FileReader("/Users/peiyang/Demo.txt");

        //自建缓冲区---数组---字符流---字符数组
        char[] cs = new char[5];
        int len=-1;

        //reader.read(字符数组)
        while((len=reader.read(cs)) != -1){//读取到的内容存放到数组中
            //System.out.println(len);
            System.out.println(new String(cs,0,len));
        }

        //关流
        reader.close();
    }
}

```



### 缓冲流（用于提供缓冲区）

BufferedReader：给字符输入流提供缓冲区 -- readLine()

```java
package cn.tedu.buffer;

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class BufferedReaderDemo {
    public static void main(String[] args) throws IOException {

        //创建对象
        //真正读取流对象的是FileReader，字符输入流
        //缓冲流对象只是给字符输入流提供一个缓冲区
        BufferedReader br = new BufferedReader(new FileReader("/Users/peiyang/Demo.txt"));

        //读取一行
        //System.out.println(br.readLine());

        String str = null;
        while((str = br.readLine()) != null){
            System.out.println(str);
        }
    }
}

```

BufferedWriter：给字符输出流提供更大的缓冲区 -- newLine() 

​		不同操作系统换行符不一样，windows：\r\n 		Linux：\n









模式 -- 遇到一类问题，提供统一的解决方式

设计模式 -- 在软件开发中，遇到一类问题，提供统一的解决方案

装饰者设计模式 -- 通过同类对象给本类对象增强功能或者完善功能





## 硬盘（二）

**字节**

往硬盘上一个txt文件写入数据  字节输出流   文件  FileOutputStream

从一个硬盘的txt文件读取数据  字节输入流   文件  FileInputStream





##  转换流

OutputStreamWriter  --  字符流转换成字节流

InputStreamWriter  --  字节流转换成字符流





## 系统流（都是字节流）

System.out

System.in

系统流都是静态的不能关流

-------------



## 打印流

底层提供打印或者打印换行功能（打印就是写出），System.out以及System.err都是打印流



## 合并流

SequenceInputStream

需要提供多个输入流对象，存储在Vector集合对象中，获取Enumeration对象，最后构建成合并流对象

合并---就是把所有的输入流的数据进行统一读取，注意输入流的编码以及格式

```java
package cn.tedu.file.sequenceInputStream;

import java.io.*;
import java.util.Enumeration;
import java.util.Vector;

public class SequenceInputStreamDemo {
    public static void main(String[] args) throws IOException {
        //提供输入流---提供待合并的数据
        FileInputStream in1 = new FileInputStream("/Users/peiyang/1.mp4");
        FileInputStream in2 = new FileInputStream("/Users/peiyang/2.mp4");
        FileInputStream in3 = new FileInputStream("/Users/peiyang/3.mp4");

        //创建Vector集合类型
        Vector<FileInputStream> v = new Vector<>();

        //把输入流对象添加到j集合中
        v.add(in1);
        v.add(in2);
        v.add(in3);

        //获取到Enummeration--把集合中的所有输入流对象存放到e对象中
        Enumeration<FileInputStream> e = v.elements();

        //创建合并流对象 --- 把含有输入流对象的e放到合并流对象中
        SequenceInputStream sis = new SequenceInputStream(e);

        //字节输出流
        FileOutputStream fos = new FileOutputStream("/Users/peiyang/123.mp4");
        //通过合并流对象统一读取数据---合并
        byte[] bs = new byte[1024*1024*10];
        int len=-1;
        while((len=sis.read(bs)) != -1){
            fos.write(bs,0,len);
        }

        sis.close();
        fos.close();


    }
}

```







## 双向流

RandomAccessFile

![截屏2021-06-25 下午8.53.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220034128.png?token=AWS37JKZDBAZJXC5X3EOQZDBTJ2U2)



可以指定文件的访问模式  -- rw 可以读可以写，操作下标时数据会自动移动，也可以指定下标进行数据操作



## 序列化与反序列化

![截屏2021-06-26 下午1.30.18](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220034476.png?token=AWS37JKLIJQF6SIXC4KYWB3BTJ2VA)

ObjectOutputSream

ObjectInputSream

![截屏2021-06-26 下午12.22.29](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220034441.png?token=AWS37JNLTUUNNS3PIACEKYLBTJ2VG)

注意：

 1. 对象要进行序列化，要保证对象对应的类实现Serializable接口

 2. 加上static或者transient的属性都不能被序列化

 3. 实现接口的这个类如果没有指定序列化版本号（serialVersionUID），java在底层就会根据属性和方法算出当时的版本号，这个算出来的版本号就会随着对象一起序列化出去，反序列化的时候就会获取序列化过来的版本号。去计算此时的类的属性和方法版本号，反序列化的时候会拿着序列化过来的版本号和刚才计算好的版本号进行比较，如果两个版本号相等就反序列化成功，如果两个版本号不相等就反序列化失败

    为了保证每次反序列化成功就要在类中指定序列化版本号（private static final long修饰）

	4. 集合和映射对象都不能序列化，只能遍历集合或者映射一一区序列化存储的对象



```java
package cn.tedu.file.serial.objectOutputStream;

import java.io.Serializable;


//想让对象进行序列化需要保证对象对应的类实现Serializable
public class Person implements Serializable {

    /**
     *  java提供的版本号
     */
    private static final long serialVersionUID = -322367462386L;
    
    //指定序列化版本号 --- 保证前后版本号一致
    //static final long serialVersionUID = 283478L;

    private String name;
    private int age;
    private char gender;

    //教室--共享--静态不能序列化
    static String classroom;

    //身高--不想序列化---手动操作
    transient double height;



    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

```







序列化

```java
package cn.tedu.file.serial.objectOutputStream;

import java.io.FileOutputStream;
import java.io.IOException;
import java.io.ObjectOutputStream;

public class ObjectOutputStreamDemo {
    public static void main(String[] args) throws IOException {

        //创建对象
        Person p = new Person();

        //给对象属性赋值
        p.setName("Lili");
        p.setAge(10);

        //开始进行序列化---创建序列化类的对象
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("p.data"));

        //写出对象
        oos.writeObject(p); //把对象进行序列化

        oos.close();


    }
}
```



反序列化

```java
package cn.tedu.file.serial.objectInputStream;

import cn.tedu.file.serial.objectOutputStream.Person;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.ObjectInputStream;

public class ObjectInputStreamDemo {
    public static void main(String[] args) throws IOException, ClassNotFoundException {

        //创建反序列化对象
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("p.data"));

        //读取对象---保证获取的是传入的对象
        Person p = (Person) ois.readObject();

        //关流
        ois.close();

        //获取对象属性
        System.out.println(p.getName()+","+p.getAge());

    }
}

```



















## 案例



1. 根据字符流实现硬盘上的文件复制

```java
package cn.tedu.file.fileReader;

import java.io.FileNotFoundException;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class FileCopy {
    public static void main(String[] args) {

        long start = System.currentTimeMillis();
        FileReader reader=null;
        FileWriter writer=null;

        try {
            //对象初始化
            reader = new FileReader("/Users/peiyang/Demo.txt");
            writer = new FileWriter("/Users/peiyang/Demo2.txt");

            //读取数据
            //自建数组缓冲区
            char[] cs = new char[1024*1024];
            int len = -1;

            while((len = reader.read(cs)) != -1){
                writer.write(cs,0,len);
            }

            writer.flush();

        } catch (IOException e) {
            e.printStackTrace();
        } finally{
            if(reader != null){
                try {
                    reader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }finally{
                    //对象置为无用对象--垃圾回收
                    reader = null;
                }
            }

            if(writer != null){
                try {
                    writer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }finally{
                    writer = null;
                }
            }

        }

        long end = System.currentTimeMillis();

        System.out.println(end-start);
    }
}

```





2. 输出工作空间中.java中的行数

```java
package cn.tedu.buffer;

import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;
import java.io.IOException;

public class JavaFileRowNum {
    public static void main(String[] args) throws IOException {

        RowNumCompute demo = new RowNumCompute("java");
        File file = new File("/Users/peiyang/IdeaProjects");

        demo.RowNumGet(file);
        System.out.println(demo.RowCount);

    }
}
 
class RowNumCompute{
    int RowCount;
    String fileType;

    public RowNumCompute(String fileType){
        RowCount = 0;
        this.fileType = fileType;
    }

    public void RowNumGet(File file) throws IOException {
        if(file.isDirectory()){
            File[] fs = file.listFiles();

            for(File f: fs){
                RowNumGet(f);
            }
        }


        if(file.getName().endsWith(fileType)){

            BufferedReader bf = new BufferedReader(new FileReader(file));

            String str = null;
            while((str = bf.readLine()) != null){
                RowCount++;
            }
        }
    }
}
```



3. 根据字节流实现硬盘上的文件复制



```java
package cn.tedu.file.inputDemo;

import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

public class FileStreamCopy {
    public static void main(String[] args) throws IOException {

        FileInputStream is = new FileInputStream("/Users/peiyang/Downloads/Tute-04-Temporal.docx");
        FileOutputStream os = new FileOutputStream("/Users/peiyang/Downloads/new/Tute-04-Temporal.docx");

        byte[] bs = new byte[1024*1024];
        int len = -1;
        while((len = is.read(bs)) != -1){
            os.write(bs,0,len);
        }

        is.close();
        os.close();

    }
}

```







![截屏2021-06-25 下午12.42.20](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-25 下午12.42.20.png)





![截屏2021-06-25 下午12.55.58](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-25 下午12.55.58.png)



![截屏2021-06-25 下午1.12.49](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-25 下午1.12.49.png)

