# Set

​	不能存储重复元素

​			实现类：HashSet、LinkedHashSet、TreeSet

​						

![截屏2021-06-21 下午3.52.06](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220032519.png?token=AWS37JIBZYO4OEFGWGGTWA3BTJ2OC)

**HashSet**

​	底层基于HashMap来实现，是由数组+链表进行存储元素，不能存储重复元素，不能保证存入数据的顺序，底层数组长度一定是2的n次幂。

​	加载因子越小：会频繁进行扩容操作，导致大量的桶没有进行存储，内存空间浪费，需要进行频繁的rehash操作降低工作效率。

​	加载因子越大：最增常长链式栈结构的长度![截屏2021-06-21 下午3.58.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220032216.png?token=AWS37JPLDNKFZ52Z2QGTX6LBTJ2OE)



![截屏2021-06-21 下午4.21.57](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220032793.png?token=AWS37JIBLM3CPA56N7CIHM3BTJ2O2)



**LinkedHashSet**

![截屏2021-06-21 下午4.45.26](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220032390.png?token=AWS37JJVIZRCQMTX3RICEA3BTJ2PC)





**TreeSet**

![截屏2021-06-21 下午4.47.36](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220032395.png?token=AWS37JPZXFATKJP55356ZO3BTJ2PO)

会自动对存储的元素进行排序---升序---自然排序

对象存储在TreeSet中想要进行排序，保证对象对应的类实现Comparable接口，重写compareTo方法去指定具体的排序规则













