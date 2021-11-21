# File



```java
package cn.tedu.file;

import java.io.File;
import java.io.IOException;

public class FileDemo1 {
    public static void main(String[] args) throws IOException {
        //创建File类对象
        //在创建对象过程中不回去监测真实的路径
        File file = new File("/Users/peiyang/newFile");

        //创建文件
        //boolean b = file.createNewFile();
        //System.out.println(b);

        //创建文件夹---目录
        boolean b1 = file.mkdirs();
        System.out.println(b1);
        //可以删除文件
        //直接删除不放在回收站
        boolean b2 = file.delete();
        System.out.println(b2);
    }
}

```



```java
package cn.tedu.file;

import java.io.File;

public class FileDemo3 {
    public static void main(String[] args) {
        File file = new File("/Users/peiyang/DemoFile");
        //返回目录或者文件名字
        System.out.println(file.getName());
        //返回所在目录路径
        System.out.println(file.getParent());
        //获取当前路径
        System.out.println(file.getPath());
    }
}

```



![截屏2021-06-22 下午11.38.30](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220033687.png?token=AWS37JLZJ4JSMILIKHS3IMTBTJ2RQ)







![截屏2021-06-22 下午11.38.30](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220033341.png?token=AWS37JO7UC75MMHV57Q3IJTBTJ2RW)

![截屏2021-06-23 下午3.16.06](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220033153.png?token=AWS37JKMEX4M732MVU4HJIDBTJ2R2)



```java
package cn.tedu.file;

import java.io.File;

public class FileDemo4 {
    public static void main(String[] args) {
        File file = new File("/Users/peiyang");

        //上次修改时间
        System.out.println(file.lastModified());
        //设置上次修改时间
        file.setLastModified(12332323232L);


        //重命名 --- 剪切（路径不一样）
        file.renameTo(new File("/Users/222"));


    }
}

```













练习：

	1. 删除文件夹下所有文件（递归方法）

```java
package cn.tedu.file;

import java.io.File;

public class DeletePractice {
    public static void main(String[] args) {

        File file = new File("/Users/peiyang/");


    }

    public static void deleteFile(File file){
        if(file.isDirectory()){
            File[] fs=file.listFiles();

            for(File f: fs){
                deleteFile(f);
            }
        }else{
            file.delete();
        }

    }
}

```



2. 统计工作空间中有多少个.java文件以及.class文件

```java
package cn.tedu.file;

import java.io.File;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class StatisticPractice {
    public static void main(String[] args) {
        File file = new File("/Users/peiyang/IdeaProjects");

        NumCompute num = new NumCompute();
        num.NumGet(file);
        Map m = num.finalNum();
        System.out.println(m);

    }



}

class NumCompute{
    int classNum;
    int javaNum;

    public NumCompute(){
        classNum = 0;
        javaNum = 0;
    }

    public void NumGet(File file){

        if(file.isDirectory()){
            File[] fs = file.listFiles();

            for(File f: fs){
                NumGet(f);
            }
        }

        if(file.getName().endsWith("class")){
            classNum++;
        }else if(file.getName().endsWith("java")){
            javaNum++;
        }

    }

    public Map<String, Integer> finalNum(){
        Map<String, Integer> map = new HashMap<>();
        map.put("classNum", classNum);
        map.put("javaNum", javaNum);
        return map;
    }
}



```



3. 输出含有数字的文件或者文件夹

```java
package cn.tedu.file;

import java.io.File;
import java.io.FileFilter;

/**
 * 输出带数字的文件或者文件夹
 */

public class NumNamePractice {
    public static void main(String[] args) {
        File file = new File("/Users/peiyang");

        File[] fs = file.listFiles(new FileFilter() {
            @Override
            public boolean accept(File pathname) {
                return pathname.getName().matches(".*\\d.*");
            }
        });
        
        //简便写法
        File[] fs1 = file.listFiles(str -> str.getName().matches(".*\\d.*"));


        for(File f: fs){
            System.out.println(f);
        }

    }

}

```

