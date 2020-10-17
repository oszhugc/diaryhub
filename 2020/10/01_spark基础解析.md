# 1 spark概述

## 1.1 什么是spark

- 定义

  spark是一种基于内存的快速, 通用, 可扩展的大数据分析引擎

- 历史

  - 2009年诞生于加州大学伯克利分校AMPLab, 项目采用scala编写
  - 2010开源
  - 2013年6月成为Apache孵化项目
  - 2014年2月成为Apache顶级项目

## 1.2 Spark内置模块

- SparkCore: 

  实现了Spark的基本功能, 包括任务调度, 内存管理, 错误恢复, 与存储系统交互等模块. Spark Core中还包含了对弹性分布式数据(Resilient Distributed DataSet, 简称RDD)的API定义

- Spark SQL

  是Spark用来操作结构化数据的程序包. 通过Spark SQL, 我们可以使用SQL或者Apache Hive版本的SQL方言(HQL)来查询数据. Spark SQL支持多种数据源, 比如Hive表, Parquest以及JSON等

- Spark Streaming

  是Spark提供的对实时数据进行流式计算的组件. 提供了用来操作数据流的API, 并且与Spark Core中的RDD API高度对应

- Spark MLlib

  提供常见的机器学习(ML)功能的程序库, 包括分类, 回归, 聚类, 协同过滤等, 还提供了模型评估, 数据导入等额外的支持功能

- 集群管理器

  Spark设计为可以高效地在一个计算节点到数千个计算节点之间的伸缩计算. 为了实现这样的要求, 同时获得最大灵活性, Spark支持在各种集群器(Cluster Manager)上运行, 包括Hadoop YARN, Apache Mesos, 以及Spark自带的一个建议调度器, 叫做独立调度器. 

Spark得到了众多大数据公司的支持, 这些公司包括Hortonworks, IBM, Intel, Cloudera, MapR, Pivotal, 百度, 阿里, 腾讯, 京东, 携程, 优酷等, 当前百度的Spark已应用与大搜索, 直达号, 百度大数据等业务; 阿里利用GraphX构建了大数据的图计算和图挖掘系统, 实现了很多生产系统的推荐算法; 腾讯Spark集群达到8000台的规模, 是当前已知的世界上最大的Spark集群. 



## 1.3 Spark特点

- 快

  与Hadoop的MapReduce相比, Spark基于内存的计算要快100倍以上, 基于硬盘的运算也要快10倍以上. Spark实现了高效的DAG执行引擎, 可以通过基于内存来高效处理数据流. 计算的中间结果是存在内存中的.

- 易用

  Spark支持Java, Python和Scala的API, 还支持超过80种高级算法, 使用户可以快速构建不同的应用. 而且Spark支持交互式的Python和Scala的Shell, 可以非常方便地在这些Shell中使用Spark集群来验证解决问题的方法

- 通用

  Spark提供了统一的解决方案. Spark可以用于批处理, 交互式查询(Spark SQL), 实时流处理(Spark Streaming), 机器学习(Spark MLlib)和图计算(GraphX). 这些不同类型的处理都可以在同一个应用中无缝使用. 减少了开发和维护的人力成本和部署平台的物理成本.

- 兼容性

  Spark可以非常方便地与其他的开源产品进行融合. 比如, Spark可以使用Hadoop的YARN和Apache Mesos作为他的资源管理和调度器, 并且可以处理所有Hadoop支持的数据, 包括HDFS, HBASE等. 这对于已经部署Hadoop集群的用户特别重要, 因为不需要做任务数据迁移就可以使用Spark的强大处理能力. 



# 2 Spark运行模式

## 2.1 Spark安装地址

- 官方地址

  http://spark.apache.org/

- 文档查看地址

  https://spark.apache.org/docs/2.1.1/

- 下载地址

  https://spark.apache.org/downloads.html

## 2.3 Local模式

### 2.3.1 概述

Local模式就是运行在一台计算机上的模式, 通常就是用于在本机上练手和测试, 他可以通过以下几种方式设置Master. 

local: 所有计算都运行在一个线程中, 没有任何并行计算, 通常我们在本机执行一些测试代码, 或者练手, 就是这种模式. 

local[K]: 指定使用几个线程来运行计算, 比如local[4]就是运行4个worker线程, 通常我们的CPU有几个core, 就指定几个线程, 最大化利用CPU的计算能力. 

local[*]: 这种模式直接帮你按照CPU最多cores来设置线程数了. 

### 2.3.2 安装使用

- 上传并解压spark安装包

  ```
  [atguigu@hadoop102 sorfware]$ tar -zxvf spark-2.1.1-bin-hadoop2.7.tgz -C /opt/module/
  [atguigu@hadoop102 module]$ mv spark-2.1.1-bin-hadoop2.7 spark
  ```

- 官方求PI案例

  ```
  [atguigu@hadoop102 sorfware]$ tar -zxvf spark-2.1.1-bin-hadoop2.7.tgz -C /opt/module/
  [atguigu@hadoop102 module]$ mv spark-2.1.1-bin-hadoop2.7 spark
  ```

  基本语法: 

  ```
  bin/spark-submit \
  --class <main-class> \
  --master <master-url> \
  --deploy-mode <deploy-mode> \
  --conf <key>=<value> \
  ...#other options
  <application-jar> \
  [application-args]
  ```

  参数说明

  --master  指定master的地址, 默认为local

  --class  你的应用的启动类(如 org.apache.spark.example.SparkPi)

  --deploy-mode  是否发布你的驱动到worker节点(cluster)或者作为一个本地客户端(client)(default:client)*

  --conf 任意的Spark配置属性, 格式key=value, 如果值包含空格, 可以加引号"key=value"

  application-jar 打包好的jar, 包含依赖, 这个URL在集群中全局可见. 比如hdfs://共享存储系统, 如果是file://path, 那么所有的节点的path都包含同样的jar

  application-args 传给main函数的参数

  --exexutor-memery 1G 指定每个executor可用内存为1G

  --total-executor-cores 2 指定每个executor使用的cup核数为2个

- 结果展示

  该算法使利用蒙特.卡罗算法求pi

- 准备文件

  ```
  [atguigu@hadoop102 spark]$ mkdir input
  ```

  在input下创建3个文件1.text, 2.text, 并且输入一下数据

  ```
  hello atguigu
  hello spark
  ```

- 启动spark-shell

  ```
  [atguigu@hadoop102 spark]$ bin/spark-shell
  Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
  Setting default log level to "WARN".
  To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
  18/09/29 08:50:52 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
  18/09/29 08:50:58 WARN ObjectStore: Failed to get database global_temp, returning NoSuchObjectException
  Spark context Web UI available at http://192.168.9.102:4040
  Spark context available as 'sc' (master = local[*], app id = local-1538182253312).
  Spark session available as 'spark'.
  Welcome to
        ____              __
       / __/__  ___ _____/ /__
      _\ \/ _ \/ _ `/ __/  '_/
     /___/ .__/\_,_/_/ /_/\_\   version 2.1.1
        /_/
           
  Using Scala version 2.11.8 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_144)
  Type in expressions to have them evaluated.
  Type :help for more information.
  
  scala>
  ```

  开启另一个CRD窗口

  ```
  [atguigu@hadoop102 spark]$ jps
  3627 SparkSubmit
  4047 Jps
  ```

  可登陆Hadoop102:4040查看程序运行

  

- 运行WordCount程序
  
```
  Scala> sc.textFile("input").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect
res0:scala>sc.textFile("input").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect
  res0: Array[(String, Int)] = Array((hadoop,6), (oozie,3), (spark,3), (hive,3), (atguigu,3), (hbase,6))

  scala>
```

  可登陆hadoop102:4040查看程序运行

###   2.3.3 提交流程

- 提交任务分析

  **Driver(驱动器)**

   Spark的驱动器是执行开发程序中的main方法的进程. 它负责开发人员编写的用来创建SparkContext, 创建RDD, 以及进行RDD的转换操作和行动操作代码的执行. 如果你使用Spark shell, 那么当你启动Spark shell的时候, 系统后台自启了一个Spark驱动器程序, 就是在Spark shell中预加载的一个叫做sc的SparkContext对象. 如果驱动器程序终止, 那么Spark应用也就结束了, 主要负责

  -  把用户程序转为任务
  - 跟踪Executor的运行状况
  - 为执行器节点调度任务
  - UI展示应用运行状况

  **Executor(执行器)**

   Spark Executor是一个工作进程, 负责在Spark作业中运行任务, 任务间互相独立. Spark应用启动时, Executor节点被同时启动, 并且始终伴随着整个Spark应用的生命周期而存在. 如果有Executor节点发生了故障或崩溃, Spark应用也可以继续执行, 会将出错节点上的任务调度到其他Executor节点上继续运行. 主要负责: 

  - 负责运行组成Spark应用的任务, 并将结果返回给驱动器进程.
  - 通过自身的块管理器(Block Manager)为用户程序中要求缓存的RDD提供内存式存储. RDD是直接存储在Executor进行内的, 因此任务可以在运行时充分利用缓存数据加速运算. 

### 2.3.4 数据流程

textFile("input")  读取本地文件input文件夹数据

flatMap(_.split(" ")) : 压平操作, 按照空格分隔符将一行数据映射成一个个单词

map((_, 1)) : 对每一个元素操作, 将单词映射为元组

reduceByKey(\_+\_) : 按照key将值进行聚合, 相加

collect : 将数据收集大Driver端展示.



##   2.4 Standalone模式

### 2.4.1 概述

构建一个由Master+Slave构成的Spark集群, Spark运行在集群中

### 2.4.2 安装使用

1. 进入spark安装目录下的conf文件夹

   ```
   [atguigu@hadoop102 module]$ cd spark/conf/
   ```

2. 修改配置文件名称

   ```
   [atguigu@hadoop102 conf]$ mv slaves.template slaves
   [atguigu@hadoop102 conf]$ mv spark-env.sh.template spark-env.sh
   ```

3. 修改slave文件, 添加work节点

   ```
   [atguigu@hadoop102 conf]$ vim slaves
   
   hadoop102
   hadoop103
   hadoop104
   ```

4. 修改spark-env.sh文件夹, 添加如下配置

   ```
   [atguigu@hadoop102 conf]$ vim spark-env.sh
   
   SPARK_MASTER_HOST=hadoop102
   SPARK_MASTER_PORT=7077
   ```

5. 分发spark包

   ```
   [atguigu@hadoop102 module]$ xsync spark/
   ```

6. 启动

   ```
   [atguigu@hadoop102 spark]$ sbin/start-all.sh
   [atguigu@hadoop102 spark]$ util.sh 
   ================atguigu@hadoop102================
   3330 Jps
   3238 Worker
   3163 Master
   ================atguigu@hadoop103================
   2966 Jps
   2908 Worker
   ================atguigu@hadoop104================
   2978 Worker
   3036 Jps
   
   ```

   

   网页查看 : hadoop102:8080

   注意 : 如果遇到"JAVA_HOME not set"异常, 可以在sbin目录下的spark-config.sh文件中

   加入如下配置: 

   ```
   export JAVA_HOME=XXXX
   ```

7. 官方求pi案例

   ```
   [atguigu@hadoop102 spark]$ bin/spark-submit \
   --class org.apache.spark.examples.SparkPi \
   --master spark://hadoop102:7077 \
   --executor-memory 1G \
   --total-executor-cores 2 \
   ./examples/jars/spark-examples_2.11-2.1.1.jar \
   100
   ```

8. 启动spark-shell

   ```
   /opt/module/spark/bin/spark-shell \
   --master spark://hadoop102:7077 \
   --executor-memory 1g \
   --total-executor-cores 2
   ```

   参数 : --master spark://hadoop102:7077 指定要连接的集群的master

9. 执行WordCount程序

   ```
   scala>sc.textFile("input").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect
   res0: Array[(String, Int)] = Array((hadoop,6), (oozie,3), (spark,3), (hive,3), (atguigu,3), (hbase,6))
   
   scala>
   ```

### 2.4.3 JobHistoryServer配置

- 修改spark-default.conf.template没名称

  ```
  [atguigu@hadoop102 conf]$ mv spark-defaults.conf.template spark-defaults.conf
  ```

- 修改spark-default.conf文件, 开启Log

  ```
  [atguigu@hadoop102 conf]$ vi spark-defaults.conf
  spark.eventLog.enabled           true
  spark.eventLog.dir               hdfs://hadoop102:9000/directory
  ```

  注意 : HDFS上的目录需要提前存在

- 修改spark-env.sh文件, 添加如下配置

  ```
  [atguigu@hadoop102 conf]$ vi spark-env.sh
  
  export SPARK_HISTORY_OPTS="-Dspark.history.ui.port=18080 
  -Dspark.history.retainedApplications=30 
  -Dspark.history.fs.logDirectory=hdfs://hadoop102:9000/directory"
  ```

  参数描述:

   spark.eventLog.dir : application在运行过程中所有的信息均记录在该属性指定的路径下

  spark.history.ui.port : 18080  WEBUI访问的端口号为18080

  spark.history.fs.logDirectory=hdfs://hadoop102:9000/directory 配置该属性后, 在start-history-server.sh时就无需再显示的指定路径, Spark History Server页面只展示该指定路径下的信息

  spark.history.retainedApplications=30  指定保存Applicaiton历史记录的个数, 如果超过这个值, 旧的应用程序信息将被删除, 这个是内存中的应用数, 而不是页面上显示的应用数. 

- 分发配置文件

  ```
  [atguigu@hadoop102 conf]$ xsync spark-defaults.conf
  [atguigu@hadoop102 conf]$ xsync spark-env.sh
  ```

- 启动历史服务

  ```
  [atguigu@hadoop102 spark]$ sbin/start-history-server.sh
  ```

- 再次执行任务

  ```
  [atguigu@hadoop102 spark]$ bin/spark-submit \
  --class org.apache.spark.examples.SparkPi \
  --master spark://hadoop102:7077 \
  --executor-memory 1G \
  --total-executor-cores 2 \
  ./examples/jars/spark-examples_2.11-2.1.1.jar \
  100
  ```

- 查看历史服务

  hadoop102:18080

### 2.4.4 HA配置

- zookeeper正常安装并启动

- 修改spark-env.sh文件添加如下配置

  ```
  [atguigu@hadoop102 spark]$ bin/spark-submit \
  --class org.apache.spark.examples.SparkPi \
  --master spark://hadoop102:7077 \
  --executor-memory 1G \
  --total-executor-cores 2 \
  ./examples/jars/spark-examples_2.11-2.1.1.jar \
  100
  ```

- 分发配置文件

  ```
  [atguigu@hadoop102 conf]$ xsync spark-env.sh
  ```

- 在hadoop102上启动全部节点

  ```
  [atguigu@hadoop102 spark]$ sbin/start-all.sh
  ```

- 在hadoop103上单独启动master节点

  ```
  [atguigu@hadoop103 spark]$ sbin/start-master.sh
  ```

- Spark HA集群访问

  ```
  /opt/module/spark/bin/spark-shell \
  --master spark://hadoop102:7077,hadoop103:7077 \
  --executor-memory 2g \
  --total-executor-cores 2
  ```

  



  

  





