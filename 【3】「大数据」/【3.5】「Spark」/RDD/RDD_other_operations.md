1. glom()

   ```python
   # Let's see how the data was divided and the data for each partition
   numPartitions = bank_rdd1.getNumPartitions()
   print(f"Total partitions: {numPartitions}")
   
   # glom(): Return an RDD created by coalescing all elements within each partition into a list
   partitions = bank_rdd1.glom().collect()
   for index,partition in enumerate(partitions):
       print(f'------ Partition {index}:')
       for record in partition:
           print(record)
   ```

   

2. max/min()

   ```python
   # Using the RDD function max(), it can be obtained in a single line 
   result_max_balance = bank_rdd_4.max(key=lambda x: x[5]) # Get max by value in index 5 (balance)
   ```

   

RDD create

 ![截屏2021-01-16 下午9.01.37](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220112621.png?token=AWS37JN3M3MQGOIIXIXD4JLBTJ7D6)



RDD operation

1. Transformation

   **filter()**

   ![截屏2021-01-16 下午9.02.48](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220112185.png?token=AWS37JIDSV3P7SAT5IUSKJLBTJ7EI)

   **map()**

   ​		![截屏2021-01-16 下午9.03.22](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220112264.png?token=AWS37JN66IDK3RE45NRR2STBTJ7EO)

   **flatMap()**

   ​		![截屏2021-01-16 下午9.03.58](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220112520.png?token=AWS37JKUIRRVE7W7BFNM35LBTJ7E6)

   **distinct()**

   ​		![截屏2021-01-16 下午9.04.47](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220112334.png?token=AWS37JLBTJZFBSW2LBESRIDBTJ7FQ)

   **union()**

   ​		![截屏2021-01-16 下午9.05.09](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220113882.png?token=AWS37JKHNFD75IE6NNOHRJLBTJ7F6)

   **intersection()**

   ​		![截屏2021-01-16 下午9.05.37](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220113375.png?token=AWS37JITECYWDECJME675NTBTJ7HI)

   **subtract()**

   ​		![截屏2021-01-16 下午9.05.57](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220113056.png?token=AWS37JJ4ATUTJSBBFZ5QPUDBTJ7GE)

   **cartesian()**

   ​		![截屏2021-01-16 下午9.06.28](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220113854.png?token=AWS37JIWBKD5MOBXB3Y5V7LBTJ7GU)		

2. motion

   **collect()**

   ​		![截屏2021-01-16 下午9.07.46](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220113583.png?token=AWS37JIJEUARY547MDKADQLBTJ7IS)

   **count()**

   ​		![截屏2021-01-16 下午9.08.05](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220113662.png?token=AWS37JJWWUAHQWZWKYVLQL3BTJ7IW)

   **countByValue()**

   ​		![截屏2021-01-16 下午9.08.24](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114226.png?token=AWS37JLK6NP47OKZV6Z7CVLBTJ7JK)

   **take()**

   ​		![截屏2021-01-16 下午9.09.16](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114124.png?token=AWS37JIJ2QAEJXNBAE6X623BTJ7JO)

   **top()**

   ​		![截屏2021-01-16 下午9.09.38](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114480.png?token=AWS37JPGONILIC3F64SBYVLBTJ7J2)

   **takeOrdered()**

   ​		![截屏2021-01-16 下午9.10.01](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114808.png?token=AWS37JN4JNRR7BIXCASD2RDBTJ7JW)

   **reduce()**

   ​		![截屏2021-01-16 下午9.10.23](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114012.png?token=AWS37JIEQI7QZOUQJHW6J5LBTJ7KI)

   **fold()**

   ​		![截屏2021-01-16 下午9.11.41](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114846.png?token=AWS37JKNRO2R5HGPEYZZALLBTJ7KO)

   **aggregate()**

   ​		![截屏2021-01-16 下午9.12.08](https://raw.githubusercontent.com/DataDevLPY/TyporaPicStore/main/Picture202111220114951.png?token=AWS37JK6EDCWEHGMG3742SDBTJ7KY)



### Tips

**1.**使用RDD.distinct() 转化操作来生成一个只包含不同元素的新RDD时需要注意，distinct() 操作的开销很大，因为它需要将所有数据通过网络进行混洗（shuffle），以确保每个元素都只有一份。此外subtract()等其他集合运rdd算中，所有使用了去重的函数都会进行混洗。

**2.**Python 会在把函数所在的对象也序列化传出去。当传递的对象是某个对象的成员，或者包含了对某个对象中一个字段的引用时（例如self.field），Spark 就会把整个对象发到工作节点上。

