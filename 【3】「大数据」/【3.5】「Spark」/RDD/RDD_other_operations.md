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

 ![截屏2021-01-16 下午9.01.37](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.01.37.png)



RDD operation

1. Transformation

   **filter()**

   ![截屏2021-01-16 下午9.02.48](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.02.48.png)

   **map()**

   ​		![截屏2021-01-16 下午9.03.22](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.03.22.png)

   **flatMap()**

   ​		![截屏2021-01-16 下午9.03.58](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.03.58.png)

   **distinct()**

   ​		![截屏2021-01-16 下午9.04.47](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.04.47.png)

   **union()**

   ​		![截屏2021-01-16 下午9.05.09](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.05.09.png)

   **intersection()**

   ​		![截屏2021-01-16 下午9.05.37](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.05.37.png)

   **subtract()**

   ​		![截屏2021-01-16 下午9.05.57](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.05.57.png)

   **cartesian()**

   ​		![截屏2021-01-16 下午9.06.28](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.06.28.png)		

2. motion

   **collect()**

   ​		![截屏2021-01-16 下午9.07.46](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.07.46.png)

   **count()**

   ​		![截屏2021-01-16 下午9.08.05](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.08.05.png)

   **countByValue()**

   ​		![截屏2021-01-16 下午9.08.24](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.08.24.png)

   **take()**

   ​		![截屏2021-01-16 下午9.09.16](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.09.16.png)

   **top()**

   ​		![截屏2021-01-16 下午9.09.38](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.09.38.png)

   **takeOrdered()**

   ​		![截屏2021-01-16 下午9.10.01](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.10.01.png)

   **reduce()**

   ​		![截屏2021-01-16 下午9.10.23](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.10.23.png)

   **fold()**

   ​		![截屏2021-01-16 下午9.11.41](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.11.41.png)

   **aggregate()**

   ​		![截屏2021-01-16 下午9.12.08](/Users/peiyang/Library/Application Support/typora-user-images/截屏2021-01-16 下午9.12.08.png)



### Tips

**1.**使用RDD.distinct() 转化操作来生成一个只包含不同元素的新RDD时需要注意，distinct() 操作的开销很大，因为它需要将所有数据通过网络进行混洗（shuffle），以确保每个元素都只有一份。此外subtract()等其他集合运rdd算中，所有使用了去重的函数都会进行混洗。

**2.**Python 会在把函数所在的对象也序列化传出去。当传递的对象是某个对象的成员，或者包含了对某个对象中一个字段的引用时（例如self.field），Spark 就会把整个对象发到工作节点上。

