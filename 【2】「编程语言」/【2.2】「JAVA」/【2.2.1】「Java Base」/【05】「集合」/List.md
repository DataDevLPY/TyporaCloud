# List：有序集合，也被称为有序列，保证存入数据有序

​			可以根据下标操作集合元素



​			实现类：

​						ArrayList，LinkedList，Vector，Stack

**ArrayList**

​				底层是由数组实现的，内存连续，默认数组的初始长度是10，根据底层右移运算进行扩容，每次在原来的基础上扩容一半



​			练习：

​					1.用数组实现ArrayList



```java
package cn.apix.collection;

import java.util.Arrays;
import java.util.List;

public class ListText1 {
    public static void main(String[] args) {

        ListArray list = new ListArray();
        list.add("abc");
        list.add("abv");
        list.add("aba");
        list.add("abp");
        list.add("abk");

        System.out.println(list.toString());
    }
}

class ListArray{
    //存储数据的数组
    String[] data;

    //元素个数以及数组下标
    int size=0;

    //无参构造---默认初始长度为10
    public ListArray(){
        //默认长度为10
        data = new String[10];
    }
    //有参构造---指定初始量
    public ListArray(int initCapacity){
        data = new String[initCapacity];
    }

    //数组扩容
    public void grow(){
        if(data.length <= 0){
            data = Arrays.copyOf(data,data.length+1);
        }else{
            //在原来的基础上长度加一半
            data = Arrays.copyOf(data,data.length + (data.length >> 1));
        }

    }

    //下标越界
    public void out(int index){
        if(index <0 | index >= size){
            throw new IllegalArgumentException("index: "+index);
        }
    }


    //添加元素
    public void add(String str){
        if(size >= data.length){
            grow();
        }

        data[size++] = str;
    }

    //插入元素
    public void add(int index, String str){
        //判断下标是否越界
        if(index <0 | index >data.length){
            throw new IllegalArgumentException("Index:"+ index);
        }
        /*
        for(int i=size-1;i>=index;i--){
            if(i == index){
                data[i] = str;
            }else{
                data[i] = data[i-1];
            }
        }

         */
        System.arraycopy(data,index,data,index+1,size-index);
        //插入这个元素
        data[index]=str;
        //元素个数
        size++;

    }

    //根据下标删除
    public void remove(int index){
        //判断是否超出范围
        out(index);

        //依次向前移动元素
        /*
        for(int i=index;i<size-1;i++){
            data[i] = data[i+1];
        }

         */
        System.arraycopy(data,index+1,data,index,size-(index+1));
        size--;
    }

    //返回第一次出现的下标值
    public int indexOf(String str){
        //遍历数组依次比较
        for(int i=0;i<size;i++){
            if((data[i] == str)| data[i] != str && data[i].equals(str)){
                return i;
            }
        }

        //数组没出现
        return -1;
    }


    //根据元素删除
    public void remove(String str){
        int index = indexOf(str);

        if(index != -1){
            remove(index);
        }

    }

    //清空集合
    public void clear(){
        size=0;
    }

    //是否包含元素
    public boolean contains(String str){
        return indexOf(str) != -1;
    }


    //获取元素
    public String get(int index){
        //越界问题
        out(index);

        return data[index];

    }

    //判断是否为空
    public boolean isEmpty(){
        return size == 0;
    }

    //替换元素
    public void set(int index, String str){
        out(index);
        data[index] = str;
    }

    //返回元素个数
    public int size(){
        return size;
    }

    //截取子列表
    public ListArray subList(int fromIndex, int toIndex){
        out(fromIndex);
        out(toIndex);

        if(fromIndex > toIndex){
            throw new IllegalArgumentException("fromIndex: "+fromIndex + ", toIndex: "+ toIndex);
        }
        //新建列表
        int count = toIndex-fromIndex;
        ListArray list = new ListArray(count);

        System.arraycopy(data,fromIndex,list.data,0,count);
        //赋值size
        list.size = count;
        return list;
    }

    //重写toString()

    @Override
    public String toString() {

        StringBuilder sb = new StringBuilder("[");
        for(int i=0;i<size;i++){
            sb.append(data[i]+", ");
        }

        String str = sb.toString();
        if(size > 0 ){
            str = str.substring(0, str.length()-2);
        }

        return str+"]";
    }
}

```





**LinkedList(链表)**



![截屏2021-06-18 下午8.05.29](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220032951.png?token=AWS37JLSDLCMPNARB3NAARTBTJ2NI)



底层是由（静态内部类）节点来进行存储元素。

底层内存不连续，不需要扩容, 增删元素效率高、查询元素效率较低

​		练习：

​				实现LinkedList



```java
package cn.apix.collection;



public class LinkedListDemo {
    public static void main(String[] args) {
        ListLinked list = new ListLinked();
        list.add("abc");
        list.add("123");
        list.add("11e");
        list.add("vhn");
        System.out.println(list);
        list.remove("abc");
        System.out.println(list);

    }
}


class ListLinked{
    //元素个数/节点数
    int size=0;
    //头节点
    Node first;
    //尾节点
    Node last;

    //  节点
    class Node{
        //存储的元素
        String item;
        //上一个节点
        Node prev;
        //下一个节点
        Node next;

        //有参构造
        public Node(String item, Node prev, Node next){
            //元素赋值给节点
            this.item = item;
            this.prev = prev;
            this.next = next;
        }
    }

    //
    public void out(int index){
        if(index < 0 || index >= size){
            throw new IllegalArgumentException("index"+ index);
        }
    }


    public Node getNode(int index){
        out(index);
        Node no = this.first;
        for(int i=0;i<=index;i++){
            no = no.next;
        }
        return no;
    }


    //添加
    public void add(String str){
        //新节点
        Node node = new Node(str,null,null);

        //判断添加元素的位置
        if(size==0){
            //表明添加的是第一个节点
            //头节点指向新节点
            this.first=node;
            this.last=node;
        }else{
            //尾部添加
            //原尾节点的下一个指向新节点
            this.last.next = node;
            //新节点的上一个指向原尾节点
            node.prev = this.last;
        }

        //尾节点指向新节点
        this.last = node;

        //节点个数+1
        size++;
    }

    //插入
    public void add(int index, String str){
        //判断下标是否越界
        if(index < 0 | index > size){
            throw new IllegalArgumentException("index" + index);
        }

        //新节点
        Node node = new Node(str,null, null);

        //判断插入节点的位置

        //插入的节点位置是头节点且size为0
        //在尾节点进行添加
        if(index == size){//w尾部添加
            add(str);
            //结束方法 保证size不会添加两次
            return;
        }

        //判断插入头节点但是已经有了头节点
        //size不为0
        if(index == 0){
            //原头节点的上一个指向新节点
            this.first.prev = node;
            //新节点的下一个指向原头节点
            node.next = this.first;

            //头节点指向新节点
            this.first = node;
        }else{
            //表明在中间插入
            //获取指定下标的节点
            /*
            Node no = this.first;
            for(int i=0;i<index;i++){
                no=no.next;
            }

             */
            Node no = getNode(index);
            //no节点的上一个节点的下一个指向新节点
            no.prev.next = node;
            //新节点的上一个指向no节点的上一个
            node.prev = no.prev;

            //no节点的上一个指向新节点
            no.prev = node;
            //新节点的下一个指向no节点
            node.next = no;

        }

        //
        size++;
    }

    //删除 -- 根据下标
    public void remove(int index){
        //越界问题
        out(index);

        //判断删除节点的位置
        //判断是否删除的是头节点
        if(index==0){
            //原头节点的下一个节点的上一个指向null
            this.first.next.prev = null;
            //头节点指向原头节点的下一个节点
            this.first = this.first.next;
        }else if(index==size-1){
            //尾部删除
            //原尾节点的上一个节点的下一个为null
            this.last.prev.next = null;
            //尾节点指向原尾节点的上一个
            this.last = this.last.prev;
        }else{
            //删除中间节点
            Node no = getNode(index);
            //no节点的下一个节点的上一个指向no的上一个
            no.next.prev = no.prev;
            no.prev.next = no.next;
        }
        //
        size--;
    }

    //根据指定元素进行删除
    public void remove(String str){
        //获取下标
        int index = indexOf(str);
        //判断是否为-1
        if(index != -1){
            remove(index);
        }
    }


    //根据元素展现第一次出现的下标
    public int indexOf(String str){
        //遍历节点找出满足要求的节点
        Node node = this.first;
        for(int i=0; i<size;i++){
            //吧每个节点的元素和参数进行比较
            if(str == node.item || str != null && str.equals(node.item)){
                return i;
            }
            node = node.next;
        }
        return -1;
    }

    @Override
    public String toString() {
        //创建对象
        StringBuilder sb = new StringBuilder("[");

        //遍历节点
        Node no = this.first;
        for(int i=0; i<size; i++){
            sb.append(no.item + ", ");
            no = no.next;

        }
        //转换字符串
        String str = sb.toString();
        //判断元素是否为空
        if(size>0){
            str = str.substring(0,str.length()-2);
        }
        return str + "]";
    }
}



```





**Vector**

底层基于数组，扩容基于三目运算符默认增加一倍，但可以按照增量

java是个集合类



​		调用elements方法返回古老的迭代器



**iterator(迭代器)**

​	底层是通过挪动指针来遍历集合与纳素，遍历过程中不能直接增删原集合元素

​	调用iterator()方法返回Iterator接口，这个iterator()方法是由Iterable接口提供的

​	对象对应的类可以实现Iterable接口，那么这个对象就可以实现迭代遍历

