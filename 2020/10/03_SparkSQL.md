# 1 SparkSQL概述

## 1.1 什么是SparkSQL

SparkSQL是Spark用来处理结构化数据的一个模块, 它提供了2个变成抽象: DataFrame和DataSet, 并且作为分布式SQL查询引擎的作用

我们已经学习了Hive, 它是将HiveSQL转换成MapReduce然后提交到集群上执行, 大大简化了编写MapReduce的程序的复杂性, 由于MapReduce这种计算模型执行效率比较慢. 所以SparkSQL应运而生, 它是将SparkSQL转换成RDD, 然后提交到集群执行, 执行效率非常快

## 1.2 Spark SQL的特点

- 易整合
- 统一的数据访问方式
- 兼容Hive
- 标准的数据连接

## 1.3 什么是DataFrame

与RDD类似, DataFrame也是一个分布式数据容器. 然而DataFrame更像传统数据库的二维表格, 除了数据以外, 还记录数据的结构信息, 即schema. 同时, 与Hive类似, DataFrame也支持嵌套数据类型(struct, array和map). 从API易用性的角度上看, DataFrame API提供的是一套高层的关系操作, 比函数式的RDD API更加友好, 门槛更低. 

上图直观地体现了DataFrame和RDD的区别. 左侧的RDD[Person]虽然以Person为类型参数, 但是Spark框架本身不了解Person类的内部结构. 而右侧的DataFrame却提供了详细的结构信息, 使得Spark SQL可以清楚地知道该数据集中包含哪些列, 每列的名称和类型各是什么. DataFrame是为了数据提供了Schema的视图. 可以把它当做数据库中的一张表来对待, DataFrame也是懒加载执行的. 性能上比RDD要高, 主要原因:

优化的执行计划: 查询计划通过Spark catalyst optimiser进行优化. 

为了说明查询优化, 我们来看上图展示的人口数据分析示例. 图中构造了两个DataFrame, 将他们join之后又做了一次filter操作. 如果原封不动地执行这个执行计划, 最终的执行效率是不高的. 因为join是一个代价较大的操作, 也可能会产生一个较大的数据集. 如果我们能将filter下推到join下方, 先对DataFrame进行过滤, 再join过滤之后的较小的结果集, 便可以有效缩短执行时间. 而Spark SQL的查询优化器正在这样做的. 简而言之, 逻辑查询计划优化就是一个利用基于关系代数的等价转换, 将高成本的操作替换为低成本操作的过程. 

## 1.4 什么是DataSet

1. 是DataFrame API的一个扩展, 是SparkSQL最新的数据抽象.
2. 用户友好的API风格, 既具有类型安全检测也具有DataFrame的查询优化特性.
3. Dataset支持编解码器, 当需要访问非堆上的数据时可以避免反序列化真个对象, 提高了效率
4. 样例类被用来在DataSet中定义数据的结构信息, 样例类中每个属性的名称直接映射到DataSet中的字段名称
5. DataFrame是DataSet的特例, DataFrame=DataSet[Row], 所以可以通过as方法将DataFrame转换为DataSet. Row是一个类型, 跟Car,Person这些的类型一样, 所有的表结构信息我都用Row表示
6. DataSet是强类型的. 比如可以有DataSet[Car], DataSet[Person]
7. DataFrame只是知道字段, 但是不知道字段的类型, 所以在执行这些操作的时候是没有办法在编译的时候检查是否类型失败的, 比如你可以对一个String进行减法操作, 在执行的时候才报错, 而DataSet不仅仅知道字段, 而且知道字段类型, 所以有更严格的错误检测. 就跟JSON对象和类对象之间的类比. 



# 2 Spark SQL编程

## 2.1 SparkSession新的起始点

在老的版本中, SparkSQL提供了两种SQL查询起始点: 一个叫SQLContext, 用于Spark自己提供的SQL查询; 一个叫HiveContext, 用于连接Hive的查询. 

SparkSession是Spark最新的SQL查询起始点, 实质上是SQLContext和HiveContext的组合, 所以在SQLContext和HiveContext上可用的API在SparkSession上同样是可以使用的.  SparkSession内部封装了SparkContext, 所以计算实际上是由SparkContext完成的. 

## 2.2 DataFrame

### 2.2.1 创建

在SparkSQL中, SparkSession是创建DataFrame和执行SQL的入门, 创建DataFrame有三种方式: 通过Spark的数据源进行创建; 从一个存在的RDD进行转换; 还可以从Hive Table进行查询返回. 

1. 从Spark数据源进行创建

   - 查看Spark数据源进行创建的文件格式

     ```
     scala> spark.read.
     csv fromat jdbc json load option options orc parquet schema table text textFile
     ```

   - 读取json文件创建DataFrame

     ```
     scala> spark.read.json("/opt/people.json")
     df: org.apache.spark.sql.DataFrame = [age:bigint, name:string]
     ```

   - 展示结果

     ```
     scala> df.show
     +----+-------+
     | age|   name|
     +----+-------+
     |null|Michael|
     |  30|   Andy|
     |  19| Justin|
     +----+-------+
     ```

2. 从RDD进行转换

3. 从Hive Table进行查询返回

### 2.2.2 SQL风格语法(主要)

1. 创建一个DataFrame

   ```
   scala> val df = spark.read.json("/opt/people.json")
   df: org.apache.spark.sql.DataFrame = [age: bigint, name: string]
   ```

2. 对DataFrame创建一个临时表

   ```
   scala> df.createOrReplaceTempView("people");
   ```

3. 通过SQL语句实现查询全表

   ```
   scala> var sqlDF = spark.sql("select * from people");
   ```

4. 结果展示

   ```
   scala> sqlDF.show
   +----+-------+
   | age|   name|
   +----+-------+
   |null|Michael|
   |  30|   Andy|
   |  19| Justin|
   +----+-------+
   ```

   注意 : 临时表是Session范围内的, Session退出后, 表就失效了. 如果想应用范围内有效, 可以使用全局表. 注意使用全局表时需要全路径访问. 如: global_temp.people

5. 对于DataFrame创建一个全局表

   ```
   scala> df.createGlobalTempView("people");
   ```

6. 通过SQL语句实现查询全表

   ```
   scala> spark.sql("select * from global_temp.people").show();
   +----+-------+
   | age|   name|
   +----+-------+
   |null|Michael|
   |  30|   Andy|
   |  19| Justin|
   
   scala> spark.newSession().sql("select * from global_temp.people").show();
   +----+-------+
   | age|   name|
   +----+-------+
   |null|Michael|
   |  30|   Andy|
   |  19| Justin|
   +----+-------+
   ```

   

### 2.2.3 DSL风格语法(次要)

1. 创建一个DataFrame

   ```
   scala> spark.read.
   csv format jdbc json load option options orc parquet schema table text textFile
   ```

2. 查看DataFrame的Schema信息

   ```
   scala> df.printSchema
   root
    |-- age: long (nullable = true)
    |-- name: string (nullable = true)
   ```

3. 只查看"name"列数据

   ```
   scala> df.select("name").show()
   +-------+
   |   name|
   +-------+
   |Michael|
   |   Andy|
   | Justin|
   +-------+
   ```

4. 查看"name"列数据以及"age+1"数据

   ```
   scala> df.select($"name", $"age" + 1).show()
   +-------+---------+
   |   name|(age + 1)|
   +-------+---------+
   |Michael|     null|
   |   Andy|       31|
   | Justin|       20|
   +-------+---------+
   ```

5. 查看"age"大于"21"的数据

   ```
   scala> df.filter($"age" > 21).show()
   +---+----+
   |age|name|
   +---+----+
   | 30|Andy|
   +---+----+
   ```

6. 按照"age"分组, 查看数据条数

   ```
   scala> df.groupBy("age").count().show()
   +----+-----+
   | age|count|
   +----+-----+
   |  19|     1|
   |null|     1|
   |  30|     1|
   +----+-----+
   ```

### 2.2.4 RDD转换为DateFrame

注意 :  如果需要RDD与DF或者DS之间操作, 那么都需要引入import spark.implicits._[spark不是包名, 而是sparkSession对象的名称]

前置条件: 导入隐式转换并创建一个RDD

```
scala> import spark.implicits._
import spark.implicits._
scala> val peopleRDD = sc.textFile("examples/people.txt")
peopleRDD: org.apache.spark.rdd.RDD[String] = ...
```

1. 通过手动确定转换

   ```
   scala> peopleRDD.map{x=>val para = x.split(",");(para(0),para(1).trim.toInt).toDF("name","age")}
   res1: org.apche.spark.sql.DataFrame = [name: string, age:int]
   ```

2. 通过反射确定

   - 创建一个样例类

     ```
     scala> case class People(name:String, age:Int)
     ```

   - 根据样例类将RDD转换成DataFrame

     ```
     scala> peopleRDD.map{x => val para=x.split(",");People(para(0),para(1).trim.toInt)}.toDF
     res2:org.apache.spark.sql.DataFrame = [name:string, age:int]
     ```

   - 通过编程方式(了解)

### 2.2.5 DataFrame转换为RDD

直接调用rdd即可. 

1. 创建一个DataFrame

   ```
   scala> val df = spark.read.json("/opt/people.json.json")
   df: org.apache.spark.sql.DataFrame = [age:bigint, name:string]
   ```

2. 将DataFrame转换为RDD

   ```
   scala> val dfToRDD = df.rdd.
   dfToRDD: org.apache.spark.rdd.RDD[org.apache.spark.sql.Row] = MapParitionsRDD[19] at rdd at <console>:29
   ```

3. 打印RDD

   ```
   scala> dfToRDD.collect
   res13: Array[org.apache.spark.sql.Row] = Array([Michael,,29],[Andy,30],[Justin,19])
   ```



## 2.3 DataSet

DataSet是具有强类型的数据集合, 需要提供对应的类型信息.

### 2.3.1 创建

1. 创建一个样类

   ```
   scala> case class Person(name:String, age:Long)
   defined class Person
   ```

2. 创建DataSet

   ```
   scala> val caseClassDS = Seq(Person("Andy", 32)).toDS()
   caseClassDS: org.apache.spark.sql.Dataset[Person] = [name: string, age: bigint]
   ```

### 2.3.2 RDD转换为DataSet

SparkSQL能够自动爱过你包含有case类的RDD转换成DataFrame, case类定义了table的结构, case类属性通过反射变成了表的列名. Case类可以包含诸如Seqs或者Array等复杂的结构

1. 创建一个RDD

   ```
   scala> val peopleRDD = sc.textFile("examples/src/main/resources/people.txt")
   peopleRDD: org.apache.spark.rdd.RDD[String] = examples/src/main/resources/people.txt MapPartitionsRDD[3] at textFile at <console>:27
   ```

2. 创建一个样例类

   ```
   scala> case class Person(name: String, age: Long)
   defined class Person
   ```

3. 将RDD转化为DataSet

   ```
   scala> peopleRDD.map(line => {val para = line.split(",");Person(para(0),para(1).trim.toInt)}).toDS
   res8: org.apache.spark.sql.Dataset[Person] = [name: string, age: bigint]
   ```

### 2.3.3 DataSet转换为RDD

调用rdd方法即可

1. 创建一个DataSet

   ```
   scala> val DS = Seq(Person("Andy", 32)).toDS()
   DS: org.apache.spark.sql.Dataset[Person] = [name: string, age: bigint]
   ```

2. 将DataSet转换为RDD

   ```
   scala> DS.rdd
   res11: org.apache.spark.rdd.RDD[Person] = MapPartitionsRDD[15] at rdd at <console>:28
   ```



## 2.4 DateFrame与DataSet的相互操作

### 2.4.1 DataFrame转Dataset

1. 创建一个dataFrame

   ```
   scala> val df = spark.read.json("examples/src/main/resources/people.json")
   df: org.apache.spark.sql.DataFrame = [age: bigint, name: string]
   ```

2. 创建一个样例类

   ```
   scala> case class Person(name: String, age: Long)
   defined class Person
   ```

3. 将DataFrame转化为DataSet

   ```
   scala> df.as[Person]
   res14: org.apache.spark.sql.Dataset[Person] = [age: bigint, name: string]
   ```

### 2.4.2 Dataset转DataFrame

1. 创建一个样例类

   ```
   scala> case class Person(name: String, age: Long)
   defined class Person
   ```

2. 创建DataSet

   ```
   scala> val ds = Seq(Person("Andy", 32)).toDS()
   ds: org.apache.spark.sql.Dataset[Person] = [name: string, age: bigint]
   ```

3. 将DataSet转化为DataFrame

   ```
   scala> val df = ds.toDF
   df: org.apache.spark.sql.DataFrame = [name: string, age: bigint]
   ```

4. 展示

   ```
   scala> df.show
   +----+---+
   |name|age|
   +----+---+
   |Andy| 32|
   +----+---+
   ```

   这种方法就是在给出每一列的类型后，使用as方法，转成Dataset，这在数据类型是DataFrame又需要针对各个字段处理时极为方便。在使用一些特殊的操作时，一定要加上 import spark.implicits._ 不然toDF、toDS无法使用。

## 2.5  RDD、DataFrame、DataSet

在SparkSQL中Spark为我们提供了两个新的抽象，分别是DataFrame和DataSet。他们和RDD有什么区别呢？首先从版本的产生上来看：

RDD (Spark1.0) —> Dataframe(Spark1.3) —> Dataset(Spark1.6)

如果同样的数据都给到这三个数据结构，他们分别计算之后，都会给出相同的结果。不同是的他们的执行效率和执行方式。

在后期的Spark版本中，DataSet会逐步取代RDD和DataFrame成为唯一的API接口。

### 2.5.1 三者的共性

1. RDD, DataFrame, DataSet全都是spark平台下的分布式弹性数据集, 为处理超大型数据提供便利
2. 三者都有惰性机制, 在进行创建,转换, 如map方法时, 不会立即执行, 只有在遇到Action如foreach时, 三者才会开始遍历运算
3. 三者都会根据spark的内存情况自动缓存运算, 这样即使数据量很大, 也不用但系会内存溢出
4. 三者都有partition的概念
5. 三者有许多共同的函数, 如filter, 排序等
6. 在对DataFrame和DataSet进行操作时, 需要这个包支持 import spark.implicits._
7. DataFrame和DataSet均可使用模式匹配获取各个字段的名字

2.5.2 



