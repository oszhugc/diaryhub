# 1 RDD概述

## 1.1 什么是RDD

RDD(Resilient Distribution Dataset), 叫做弹性分布式数据集, 是Spark中最基本的数据抽象. 代码中是一个抽象类, 他代表一个弹性的, 不可变, 可分区, 里面的元素可并行计算的集合. 

## 1.2 RDD属性

- 一组分区(partition), 即数据的基本组成单位
- 一个计算每个分区的函数
- RDD之间的依赖关系
- 一个Partitioner, 即RDD的分片函数
- 一个列表, 存储存取每个Partition的优先位置(preferred location)

## 1.3 RDD特点

RDD表示只读的分区的数据集, 对RDD进行改动, 只能通过RDD的转换操作, 由一个RDD得到一个新的RDD, 新的RDD包含了从其他RDD衍生所必需的信息. RDDs之间存在依赖, RDD的执行是按照血缘关系延时计算的. 如果血缘关系较长, 可以通过持久化RDD来切断血缘关系. 

### 1.3.1 弹性

- 存储的弹性 : 内存和磁盘的自动切换
- 容错的弹性 : 数据丢失可以自动恢复
- 计算的弹性 : 计算出错重试机制
- 分片的弹性: 可根据需要重新分片

### 1.3.2 分区

RDD逻辑上是分区的, 每个分区的数据是抽象的存在, 计算的时候会通过一个compute函数得到每个分区的数据. 如果RDD是通过已有的文件系统构建, 则compute函数是读取指定文件系统中的数据, 如果RDD是通过其他RDD转换而来, 则compute函数是执行转换逻辑将其他RDD的数据进行转换. 

### 1.3.3 只读

RDD是只读的, 想要改变RDD中的数据, 只能在现有的RDD基础上创建新的RDD

由一个RDD转换到另一个RDD, 可以通过丰富的操作算子实现, 不再像MapReduce那样只能写Map和reduce了.

RDD的操作算子包括两类:一类叫做transformations, 它是用来将RDD进行转化, 构建RDD的血缘关系; 另一类叫做action, 他是用来触发RDD的计算, 得到RDD的相关计算结果或者将RDD保存得到文件系统中. 

### 1.3.4 依赖

RDDs通过操作算子进行转换, 转换得到新的RDD包含了从其他RDDs衍生所必需的信息, RDDs之间维护着这种血缘关系, 也称为依赖. 依赖包含两种: 一种是窄依赖, RDDs之间分区是一一对应的, 另一种是宽依赖, 下游RDD的每个分区与上游RDD(也称为父RDD)的每个分区有关系, 是多对多的关系. 

### 1.3.5 缓存

如果应用程序中多次使用同一个RDD, 可以将RDD缓存起来, 该RDD只有在第一次计算的时候会根据血缘关系得到分区的数据, 在后续其他地方用到该RDD的时候, 会直接从缓存处取而不用再根据血缘关系计算, 这样就加速 后期的重用. 

### 1.3.6 CheckPoint

虽然RDD的血缘关系天然的可以实现容错, 当RDD的某个分区数据失败或丢失, 可以通过血缘关系重建. 但是对于长时间迭代型应用来说, 随着迭代的进行, RDDs之间的血缘关系会越来越长, 一旦在后续迭代过程中出错, 则需要通过非常长的血缘关系去重建, 势必影响性能. 为此, RDD支持checkpoint将数据保存到持久化的存储中, 这样就可以切断之前的血缘关系, 因为checkpoint后的RDD不需要知道它的父RDDs了, 它可以从checkpoint处拿到数据. 



# 2 RDD编程

## 2.1 编程模型

在Spark中, RDD被表示为对象, 通过对象上的方法调用来对RDD进行转化. 经过一系列的transformations定义RDD之后, 就可以调用actions触发RDD的计算, action可以是向应用程序返回结果(count, collect等), 或者向存储系统保存数据(saveAsTextFile等). 在Spark中, 只有遇到action, 才会执行RDD的计算(即延迟计算), 这样在运行时可以通过管道的方式传输多个转换. 

要使用Spark, 开发者需要编写一个Driver程序, 他被提交到集群以调度运行Worker. Driver中定义了一个或者多个RDD, 并调用RDD上的action, Worker则执行RDD分区计算任务. 

## 2.2 RDD创建

在Spark中创建RDD的方式可以分成三种, 从集合中创建RDD; 从外部存储创建RDD, 从其他RDD创建

### 2.2.1 从集合中创建

从集合中创建RDD, Spark主要提供了两种函数: parallelize和makeRDD

- 使用parallelize()从集合创建

  ```
  scala> val rdd = sc.parallelize(Array(1,2,3,4,5,6,7,8))
  rdd: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[0] at parallelize at <console>:24
  ```

- 使用makeRDD()从集合创建

  ```
  scala> val rdd1 = sc.makeRDD(Array(1,2,3,4,5,6,7,8))
  rdd1: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[1] at makeRDD at <console>:24
  ```

### 2.2.2 由外部存储系统的数据集创建

包括本地的文件系统, 还有所有hadoop支持的数据集, 比如hdfs, Cassandra, HBASE等, 

我们会在后面详细介绍

```
scala> val rdd2= sc.textFile("hdfs://hadoop102:9000/RELEASE")
rdd2: org.apache.spark.rdd.RDD[String] = hdfs://hadoop102:9000/RELEASE MapPartitionsRDD[4] at textFile at <console>:24
```

### 2.2.3 从其他RDD创建

## 2.3 RDD的转换(面试开发重点)

RDD整体上分为Value类型和key-value类型

### 2.3.1 Value类型

#### map(func)案例

1. 作用 : 返回一个新的RDD, 该RDD由每一个输入元素经过func函数转换后组成

2. 需求 : 创建一个1-10数组的RDD, 将所有元素*2形成新的RDD

   - 创建

     ```
     scala> var source  = sc.parallelize(1 to 10)
     source: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[8] at parallelize at <console>:24
     ```

   - 打印

     ```
     scala> source.collect()
     res7: Array[Int] = Array(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
     ```

   - 将所有元素*2

     ```
     scala> val mapadd = source.map(_ * 2)
     mapadd: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[9] at map at <console>:26
     ```

   - 打印最终结果

     ```
     scala> mapadd.collect()
     res8: Array[Int] = Array(2, 4, 6, 8, 10, 12, 14, 16, 18, 20)
     ```

#### mapPartitons()案例

1. 作用 : 类似于map, 但独立地在RDD的每个分区上运行, 因此在类型为T的RDD上运行时, func函数类型必须是Iterator[T]=>Iterator[U]. 假设有N个元素, 有M个分区, 那么map的函数的将被调用N次, 而mapPartitions被调用M次, 一个函数一次处理所有分区. 

2. 需求 : 创建一个RDD, 使每个元素*2组成新的RDD

   ```
   scala> rdd.mapPartitions(x=>x.map(_*2))
   res3: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[6] at mapPartitions at <console>:27
   ```

3. 打印新的RDD

   ```
   scala> res3.collect
   res4: Array[Int] = Array(2, 4, 6, 8)
   ```

#### mapPartinsWithIndex()案例

1. 作用 : 类似于mapPartitions, 但是func带有一个整数参数表示分片的索引值, 因此在类型为T的RDD上运行时, func的函数类型必须是(Int, Interator[T])=>Iterator[U]

2. 需求 : 创建一个RDD, 使每个元素跟所在分区形成一个元组组成一个新的RDD

   - 创建一个RDD

     ```
     scala> val rdd = sc.parallelize(Array(1,2,3,4))
     rdd: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[4] at parallelize at <console>:24
     ```

   - 使每个元素跟所在分区形成一个元组组成一个新的RDD

     ```
     scala> val indexRdd = rdd.mapPatitionsWithIndex((index, items) => (items.map(index, _)))
     indexRdd: org.apache.spark.rdd.RDD[(Int, Int)] = MapPartitionsRDD[5] at mapPartitionsWithIndex at <console>:26
     ```

   - 打印新的RDD

     ```
     Scala> indexRdd.collect
     res2: Array[(Int, Int)] = Array((0,1), (0,2), (1,3), (1,4))
     ```

#### flatMap()案例

1. 作用 : 类似于map, 但是每个输入元素可以被映射为0或多个输出元素(所以func应该返回一个序列, 而不是单一元素)

2. 需求 :  创建一个元素为1-5的RDD, 运行flatMap创建一个新的RDD, 新的RDD为原RDD的每个元素的扩展(1->1, 2->1,2 ...... 5->1,2,3,4,5)

   - 创建

     ```
     scala> var sourceFlat = sc.parallelize(1 to 5)
     sourceFlat: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[12] at parallelize at <console>:24
     ```

   - 打印

     ```
     scala> sourceFlat.collect()
     res11: Array[Int] = Array(1, 2, 3, 4, 5)
     ```

   - 根据原RDD创建新RDD(1->1,2->1,2.....5->1,2,3,4,5)

     ```
     Scala> val flatMap = sourceFlat.flatMap(1 to _)
     flatMap: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[13] at flatMap at <console>:26
     ```

   - 打印新RDD

     ```
     scala> flatMap.collect()
     res12: Array[Int] = Array(1, 1, 2, 1, 2, 3, 1, 2, 3, 4, 1, 2, 3, 4, 5)
     ```

#### map()和mapPartitons()

1. map() : 每次处理一条数据
2. mapPartition() : 每次处理一个分区的数据, 这个分区的数据处理完后, 原RDD中分区的数据才能释放, 可能导致OOM
3. 开发指导 : 当内存空间较大的时候建议使用mapPartition(), 以提高处理效率

#### glom案例

1. 作用 : 将每个分区形成一个数组, 形成新的RDD类型时RDD[Array[T]]

2. 需求 : 创建一个4个分区的RDD, 并将每个分区的数据放到一个数组

   - 创建

     ```
     Scala> var rdd = sc.parallelize(1 to 16, 4)
     rdd: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[65] at parallelize at <console>:24
     ```

   - 将每个分区的数据放到一个数组并收集到Driver端打印

     ```
     Scala> rdd.glom().collect()
     res25: Array[Array[Int]] = Array(Array(1, 2, 3, 4), Array(5, 6, 7, 8), Array(9, 10, 11, 12), Array(13, 14, 15, 16))
     ```

#### groupBy()案例

1. 作用 : 分组, 按照传入函数的返回值进行分组, 将相同的key对应的值放入一个迭代器,

2. 需求 : 创建一个RDD, 按照元素模以2的值进行分组

   - 创建

     ```
     Scala> var rdd = sc.parallelize(1 to 4)
     rdd: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[65] at parallelize at <console>:24
     ```

   - 按照元素模以2的值进行分组

     ```
     Scala> var group = rdd.groupBy(_%2)
     group: org.apache.spark.rdd.RDD[(Int, Iterable[Int])] = ShuffledRDD[2] at groupBy at <console>:26
     ```

   - 打印结果

     ```
     scala> group.collect
     res0: Array[(Int, Iterable[Int])] = Array((0,CompactBuffer(2, 4)), (1,CompactBuffer(1, 3)))
     ```

#### filter()案例

1. 作用 : 过滤. 返回一个新的RDD, 该RDD经由func函数计算后返回值为true的输入元素组成

2. 需求 : 创建一个RDD(由字符串组成), 过滤出一个新的RDD(包含"xiao"子串)

   - 创建

     ```
     Scala> var sourceFilter = sc.parallelize(Array("xiaoming", "xiaojiang", "xiaowang", "dazhi"))
     sourceFilter: org.apache.spark.rdd.RDD[String] = ParallelCollectionRDD[10] at parallelize at <console>:24
     ```

   - 打印

     ```
     Scala> sourceFilter.collect()
     res9: Array[String] = Array(xiaoming, xiaojiang, xiaohe, dazhi)
     ```

   - 过滤出含有"xiao"子串的形成一个新的RDD

     ```
     Scala> var filter = sourceFilter.filter(_.contains("xiao"))
     filter: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[11] at filter at <console>:26
     ```

   - 打印新RDD

     ```
     scala> filter.collect()
     res10: Array[String] = Array(xiaoming, xiaojiang, xiaohe)
     ```

     