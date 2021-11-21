# Set

​	不能存储重复元素

​			实现类：HashSet、LinkedHashSet、TreeSet

​						

![截屏2021-06-21 下午3.52.06](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-21 下午3.52.06.png)

**HashSet**

​	底层基于HashMap来实现，是由数组+链表进行存储元素，不能存储重复元素，不能保证存入数据的顺序，底层数组长度一定是2的n次幂。

​	加载因子越小：会频繁进行扩容操作，导致大量的桶没有进行存储，内存空间浪费，需要进行频繁的rehash操作降低工作效率。

​	加载因子越大：最增常长链式栈结构的长度![截屏2021-06-21 下午3.58.36](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-21 下午3.58.36.png)



![截屏2021-06-21 下午4.21.57](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-21 下午4.21.57.png)



**LinkedHashSet**

![截屏2021-06-21 下午4.45.26](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-21 下午4.45.26.png)





**TreeSet**

![截屏2021-06-21 下午4.47.36](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-06-21 下午4.47.36.png)

会自动对存储的元素进行排序---升序---自然排序

对象存储在TreeSet中想要进行排序，保证对象对应的类实现Comparable接口，重写compareTo方法去指定具体的排序规则













