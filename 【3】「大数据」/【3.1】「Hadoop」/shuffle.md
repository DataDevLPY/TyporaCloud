### 1. 什么是shuffle

```
把父RDD中的KV对按照Key重新分区，从而得到一个新的RDD。也就是说原本同属于父RDD同一个分区的数据需要进入到子RDD的不同的分区。
```

### 2. 为什么需要shuffle？

```
在分布式计算框架中，数据本地化是一个很重要的考虑，即计算需要被分发到数据所在的位置，从而减少数据的移动，提高运行效率。

Map-Reduce的输入数据通常是HDFS中的文件，所以数据本地化要求map任务尽量被调度到保存了输入文件的节点执行。但是，有一些计算逻辑是无法简单地获取本地数据的，reduce的逻辑都是如此。对于reduce来说，处理函数的输入是key相同的所有value，但是这些value所在的数据集(即map的输出)位于不同的节点上，因此需要对map的输出进行重新组织，使得同样的key进入相同的reducer。 shuffle移动了大量的数据，对计算、内存、网络和磁盘都有巨大的消耗，因此，只有确实需要shuffle的地方才应该进行shuffle，否则尽可能避免shuffle。
```

### 3. 什么时候shuffle?

```
1)去重操作：
	Distinct等。
2)聚合，byKey类操作
	reduceByKey、groupByKey、sortByKey等。
	byKey类的操作要对一个key，进行聚合操作，那么肯定要保证集群中，所有节点上的相同的key，移动到同一个节点上进行处理。
3)排序操作
	sortByKey等。
4)重分区操作
	repartition、repartitionAndSortWithinPartitions、coalesce(shuffle=true)等。
	重分区一般会shuffle，因为需要在整个集群中，对之前所有的分区的数据进行随机，均匀的打乱，然后把数据放入下游新的指定数量的分区内。
5)集合或者表操
	join、cogroup等。
	两个rdd进行join，就必须将相同join key的数据，shuffle到同一个节点上，然后进行相同key的两个rdd数据的笛卡尔乘积。
```

