# Stream

用于操作集合元素的流式结构

提供了大量的函数式接口方便使用



```java
package cn.apix.collection.stream;

import java.util.ArrayList;
import java.util.List;
import java.util.function.Consumer;
import java.util.function.Predicate;
import java.util.stream.Stream;

public class streamDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();

        list.add("C");
        list.add("python");
        list.add("C++");
        list.add("Java");
        list.add("VB");

        //筛选集合中出以C开头的元素
        /*
        for(String string:list){
            if(string.startsWith("C")){
                System.out.println(string);
            }
        }

         */

        //获取流式结构
        Stream<String> s = list.stream();
        /*
        s.filter(new Predicate<String>() {

            //重写方法进行筛选元素
            @Override
            public boolean test(String t) {
                return t.startsWith("C");
            }
        }).forEach(new Consumer<String>() {

            //重写方法把筛选到的元素进行输出
            @Override
            public void accept(String t) {
                System.out.println(t);
            }
        });
         */

        System.out.println("==========");


        //lambda表达式
        s.filter(str->str.startsWith("C")).
                sorted((str1,str2)->str2.compareTo(str1)).
                        forEach(str->System.out.println(str));


        //获取匹配个数
        //System.out.println(s.filter(str->str.startsWith("C")).count());



    }
}

```

