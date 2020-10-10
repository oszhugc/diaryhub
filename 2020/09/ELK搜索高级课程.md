## 1. 课程简介

### 1.1 课程内容

ELK是包含但不限于ElasticSearche(简称es),LogStash,Kibana三个开源软件的组成的一个整体. 这三个软件合成ELK. 是用于数据抽取(logstash), 搜索分析(es), 数据展示(kibana)的一整套解决方案, 所以也称作ELK Stack. 

本课程从分别对三个组件进行详细介绍, 尤其是es, 因为他是ELK的核心. 本课程从es底层对文件, 索引, 搜索, 聚合, 集群进行介绍, 从搜索和聚合分析实例来展示es的魅力. logstash从内部如何采集数据到指定地方来展示他数据采集的功能. kibana则从数据绘制展示数据可视化的功能. 

### 1.2 面向的人员

- java工程师: 深入研究es, 使得java工程师向搜索工程师买进
- 运维工程师: 搭建整体elk集群. 不需写代码, 仅需要配置, 即可手机服务器指标, 日志文件, 数据库数据, 并在前端华丽展示. 
- 数据分析人员: 不需要写代码, 仅需要配置kibana图表, 即可完成数据可视化工作, 得到想要的数据图表
- 大厂架构师: 完成数据中台的搭建, 对公司数据流的处理得心应手, 对接本公司大数据业务

### 1.3 课程优势

- 基于最新的elk7.3版本讲解. 最新api. 包含sql功能
- 理论和实际代码相辅相成. 理论结合画图讲解. 代码与springboot结合
- 包含实际运维部署理论和实践
- ELK整体流程项目, 包含数据采集

### 1.4 学习路径

参照目录, 按照介绍, es入门, 文档, 映射, 索引, 分词器, 搜索, 聚合, logstash, kibana. 集群部署, 项目实战. 





## 2 Elastick Stack简介

### 2.1 简介

ELK是一个免费开源的日志分析架构技术栈总成. 官网http://www.elstic.co/cn. 包含三大基础组件, 分别是elasticsearch, logstash, kibana. 但实际上elk不仅仅使用于日主分析. 他还可以支持其他任何数据搜索, 分析和收集的场景, 日志分析和收集只是更具有代表性. 并非唯一性. 



随着elk的发展, 又有新成员beats, elasticCloud的加入, 所以就形成了elastic stack. 所以说, elk是旧称呼, elastic stack是新的名字. 



### 2.2 特色

- 处理方式灵活: elasticsearch是目前最流行的准实时全文检索引擎, 具有高速检索大数据的能力
- 配置简单: 安装elk的每个组件, 仅需要配置每个组件的一个配置文件就可以. 修改处不多, 因为大量参数已经默认配置在系统中, 修改想要修改的选项就可以. 
- 接口简单: 采用json形式的Restful api接收数据并响应, 无关语言
- 性能高效: elasticsearch基于优秀的全文检索技术Lucene, 采用倒排索引, 可以轻松地在百亿级别数据量下, 搜索出想要的内容, 并且是秒级响应. 
- 灵活扩展: elasticsearch和logstash都可以根据集群规模线性拓展, elasticsearch内部自动实现集群协作
- 数据展示华丽: kibana作为前端展示工具, 图表华丽, 配置简单. 



### 2.3 组件介绍

#### elasticsearch

elasticsearch是使用java语言开发, 基于Lucene, 分布式,通过restful方式进行交互的近实时搜索平台框架. 他的特点有: 分布式, 零配置, 自动发现, 索引自动分片, 索引副本机制, restful风格接口, 多数据源, 自动搜索负载等

#### logstash

logstash基于java开发, 是一个数据抽取转化工具, 一般工作方式为c/s架构, client端安装在需要手机信息的主机上, server端负责将受到的各节点日志进行过滤,修改等操作在一并发往es或其他组件上

#### kibana

kibana基于node.js, 也是一个开源和免费的可视化工机具. kibana可以为logstash和es提供日志分析友好的web界面, 可以汇总, 分析和搜索重要数据日志. 

#### beats

beats平台集合了多种单一用途数据采集器. 它们从成千上百的机器和系统向logstash或es发送数据. 

beats由如下组成: 

- packetbeat: 轻量型网络数据采集器, 用于深挖网线上传输的数据, 了解应用程序动态.  packetbeat是一款轻量型网络数据包分析器, 能够将数据发送到logstash或者es. 其支持icmp, dns, http, mysql, postgresql, redis, mongodb, memcache等协议
- filebeat: 轻量级的日志采集器. 当您要面对成千上万的服务器, 虚拟机和容器生成的日志时, 请告别ssh吧,  filebeat将为您提供一种轻量型方法, 用于转发和汇总日志与文件, 让简单的事情不在复杂. 
- metribeat: 轻量型指标采集器. metricbeat能够以一种轻量型的方式, 输送各种系统和服务统计数据, 从CPU到内存, 从redis到nginx, 不一而足. 可定期获取外部系统的监控指标信息. 其中可以监控, 收集apache http, Haproxy, mongodb, mysql, nginx,redis, system,zk等服务.
- winlogbeat: 轻量级Windows事件日志采集器. 用于密切监控基于windows的基础设置上发生的事件. winlogbeat能够以一种轻量型的方式, 将windows事件日志实时地流式传输到es和logstash.
- auditbeat: 轻量级审计日志采集器, 收集您linux审计框架的数据, 监控文件完整性. auditbeat实时采集这些事件, 然后发送到elastic stack 其他  部分进行分析
- heartbeat: 面向运行状态监控的轻量级采集器. 通过主动探测来检测服务的可用性. 通过给定url列表, heartbeat仅仅询问: 网站运行正常吗? heartbeat会将此信息和响应时间发送至elastic的其他部分, 进一步分析. 
- functionbeat: 面向云端数据的无服务器采集器. 在作为一项功能部署在云服务提供商的功能即服务(faas)平台上, functionbeat即能采集, 传输并监测来自您云服务的相关数据. 

#### elastic cloud

基于es的软件及服务(saas)解决方案. 通过elastic的官方合作伙伴使用托管的es服务. 



## 3 elasticsearch是什么

### 3.1 搜索是什么

概念: 用户输入想要的关键词, 返回包含有该关键词的所有信息

场景:

	1. 互联网搜索:谷歌, 百度, 各种新闻首页
 	2. 站内搜索(垂直搜索): 企业oa查询订单, 人员, 部门, 电商网站内部搜索商品(淘宝, 京东)场景

### 3.2 数据库做搜索弊端

#### 3.2.1 站内搜索

- 存储问题: 电商网站商品上亿条时, 涉及到单表数据过大必须拆分表, 数据库磁盘占用过大必须分库(mycat)
- 性能问题:解决上面问题后, 查询"笔记本电脑"等关键词时, 上亿条数据的商品名称需要逐行扫描, 性能跟不上. 
- 不能分析. 如搜索"笔记本电脑", 只能搜索完全和关键词一样的数据, 那么数据量小时, 搜索"笔记本电脑", "电脑"数据要不要给用户

#### 3.2.2 互联网搜索

数据量太大, pb级别



### 3.3 全文检索, 倒排索引和lucene

#### 全文检索

倒排索引. 数据存储时, 经行分词建立term索引库. 

倒排索引源于实际应用中需要根据属性的值来查找记录. 这种索引表的每一项都包括一个属性值和具体该属性值的各记录的地址. 由于不是由记录来确定属性值, 而是由属性值来确定记录的位置, 因而成为倒排索引(inverted index). 带有倒排索引的文件我们称为倒排索引文件, 简称倒排文件(inverted file)

#### lucence

就是一个jar包, 里面封装了全文检索的引擎, 搜索的算法代码. 开发时,, 引入Lucene的jar包,通过api开发搜索相关业务. 底层全在磁盘建立索引库. 



### 3.4 什么是elasticsearch

#### 简介

官网: https://www.elastic.co/cn/products/elasticsearch

#### ElasSearch的功能:

- 分布式的搜索引擎和数据分析引擎

  搜索:互联网搜索, 电商网站站内搜索, oa系统查询

  数据分析:  电商网站查询近一周哪些品类的图书销售前十; 新闻网站, 最近3天阅读量最高的是个关键词, 舆情分析

- 全文检索, 结构化检索, 数据分析

  全文检索: 搜索商品名称包含java的图书 select * from books where book_name like '%java%'

  结构化检索:搜索商品分类为spring的图书  select * from books where category = 'spring'

  数据分析: 分析每一个分类下有多少种图书, select category, count(*) from books group by category

- 对海量数据进行近实时处理

  分布式: es自动将海量数据分散到多台服务器上去存储和检索, 经行并行查询, 踢桃搜索效率. 相对的, lucene是单机应用

  近实时: 数据库上亿条数据查询, 搜索一次耗时几个小时, 是批处理. 而es只需要秒级即可查询海量数据, 所以叫近实时, 秒级. 



#### ElasticSearch的使用场景:

国外: 

- 维基百科
- stack over flow 
- github
- 日志分析
- 商品价格监控网站
- bi系统

国内

- 百度
- oa, erp系统站内搜索



#### ElasticSearch的特点

- 可拓展性: 大型分布式集群技术, 处理pb级别数据, 大公司可以使用. 小公司数据量小, 也可以部署在单机. 大数据领域使用广泛. 
- 技术整合: 将全文检索, 数据分析, 分布式相关技术整合在一起: lucene. 商用的数据分析软件(BI软件), 分布式数据库(mycat)
- 部署简单: 开箱即用, 很多默认配置不需要关心, 解压完直接运行即可. 拓展时, 只需要多部署几个实例即可, 负载均衡, 分片迁移集群内部自己实施. 
- 接口简单: 使用restful api进行交互, 跨语言. 
- 功能强大: es作为传统数据库的一个补充, 提供了数据库所不能提供的很多功能, 如全文哎你说, 同义词处理, 相关度排名. 



### 3.5 elasticSearch核心概念

#### 3.5.1 lucene和es的关系

Lucene: 最先进, 功能最强大的搜索库, 直接基于Lucene开发, 非常复杂, API复杂. 

es: 基于Lucene, 封装了许多Lucene底层功能, 提供简单易用的restful api接口和许多语言的客户端, 如java的高级客户端和底层客户端. 

#### 3.5.2 elasticsearch的核心概念

##### 1 NRT(Near RealTime) 近实时

两方面: 

- 写入数据时, 过1秒才会被搜索到, 因为内部在分词和录入索引
- es搜索时: 搜索和分析数据需要秒级出结果

##### 2 cluster 集群

包含一个或多个启动着es实例的机器群. 通过一台机器起一个es实例. 同一网络下, 集群名一样的多个es实例自动组成集群, 自动均衡分片等行为. 默认集群命名为'elasticsearch'.

##### 3 node 节点

每个es实例称为一个节点, 节点名自动分配, 也可以手动配置

##### 4 index 索引

包含一堆有相似结构的文档数据

索引创建规则

- 仅限小写字母
- 不能包含\, /, *, ?, ", <, >, |, #以及空格等特殊符号
- 从7.0版本开始不再包含冒号
- 不能以-, _或者+开头
- 不能超过255个字节

##### 5 document 文档

es中的最小数据单元. 一个document就像数据库中的一条记录. 通常以json格式显示. 多个document存储于一个索引(index)中.

```
book document

{
  "book_id": "1",
  "book_name": "java编程思想",
  "book_desc": "从Java的基础语法到最高级特性（深入的[面向对象](https://baike.baidu.com/item/面向对象)概念、多线程、自动项目构建、单元测试和调试等），本书都能逐步指导你轻松掌握。",
  "category_id": "2",
  "category_name": "java"
}
```



##### 6 field 字段

就像数据库中的列(columns), 定义每个document应该有的字段

##### 7 type 类型

每个索引里都可以有一个或多个type, type是index中的一个逻辑数据分类, 一个type下的document, 都要相同的field.

注意: 6.0之前的版本都有type概念, type相当于关系数据库中的表. es官网将在es9.0中彻底删除type, 本教程type都为_doc

##### 8 shard 分片

index数据过大时, 将index里面的数据, 分为多个shard, 分布式的存储在各个服务器上面, 可以支持海量数据和高并发, 提升性能和吞吐量, 充分利用多天机器cpu. 

##### 9 replica 副本

在分布式环境下, 任何一台机器都会随时宕机, 如果宕机, index的一个分片没有, 导致此index不能搜索. 所以, 为了保证数据的安全, 我们都会将每个一你的写的分片进行备份, 存储在另外的机器上. 保证少数机器宕机es集群仍可以搜索. 

能正常提供查询和插入的分片我们称为主分片(primary shard), 其余的我们就管他们叫做备份的分片(replica shard). 

es6默认新建索引时, 5分片, 2副本,也就是一主一从共10个分片. 所以, es集群最小规模为2台. 



#### 3.5.3 es核心概念 vs. 数据库核心概念

| 关系型数据库    | es                    |
| --------------- | --------------------- |
| 数据库 database | 索引 index            |
| 表 table        | 索引 index (原为type) |
| 数据行 row      | 文档 document         |
| 数据列 column   | 字段 field            |
| 约束 schema     | 映射 mapping          |



## 4 ElasticSearch相关软件安装

### 4.1 windows安装es

1. 安装jdk, 至少1.8.0_73以上版本, 验证: java -version

2. 下载和解压es, 查看目录结构

   https://www.elastic.co/cn/downloads/elasticsearch

   - bin : 脚本目录, 包括: 启动, 停止等可执行脚本
   - config : 配置文件目录
   - data : 索引目录, 存放索引文件的地方
   - logs : 日志目录
   - modules : 模块目录, 包括了es的功能模块
   - plugins: 插件目录, es支持插件机制

3. 配置文件

   位置: 

   - 使用zip, tar安装, 配置文件的地址在安装目录的config下
   - 使用rpm安装, 配置文件在/etc/elasticsearch/下
   - 使用msi安装, 配置文件的地址在安装目录的config下, 并且会自动将config目录地址写入环境变量ES_PATH_CONF

   elasticsearch.yml

   配置格式是yaml, 可以采用如下两种方式: 

   方式1: 层次方式

   ```
   path:
       data: /var/lib/elasticsearch
       logs: /var/log/elasticsearch
   ```

   方式2: 属性方式

   ```
   path.data: /var/lib/elasticsearch
   path.logs: /var/log/elasticsearch
   ```

   常见的配置项如下:

   ```
   cluster.name: 
   	配置elasticsearch的集群名称，默认是elasticsearch。建议修改成一个有意义的名称。
   node.name:
   	节点名，通常一台物理服务器就是一个节点，es会默认随机指定一个名字，建议指定一个有意义的名称，方便管理
   	一个或多个节点组成一个cluster集群，集群是一个逻辑的概念，节点是物理概念，后边章节会详细介绍。
   path.conf: 
   	设置配置文件的存储路径，tar或zip包安装默认在es根目录下的config文件夹，rpm安装默认在/etc/ elasticsearch
   path.data:
   	设置索引数据的存储路径，默认是es根目录下的data文件夹，可以设置多个存储路径，用逗号隔开。
   path.logs:
   	设置日志文件的存储路径，默认是es根目录下的logs文件夹
   path.plugins: 
   	设置插件的存放路径，默认是es根目录下的plugins文件夹
   bootstrap.memory_lock: true
   	设置为true可以锁住ES使用的内存，避免内存与swap分区交换数据。
   network.host: 
   	设置绑定主机的ip地址，设置为0.0.0.0表示绑定任何ip，允许外网访问，生产环境建议设置为具体的ip。
   http.port: 9200
   	设置对外服务的http端口，默认为9200。
   transport.tcp.port: 9300  集群结点之间通信端口
   node.master: 
   	指定该节点是否有资格被选举成为master结点，默认是true，如果原来的master宕机会重新选举新的master。
   node.data: 
   	指定该节点是否存储索引数据，默认为true。
   discovery.zen.ping.unicast.hosts: ["host1:port", "host2:port", "..."]
   	设置集群中master节点的初始列表。
   discovery.zen.ping.timeout: 3s
   	设置ES自动发现节点连接超时的时间，默认为3秒，如果网络延迟高可设置大些。
   discovery.zen.minimum_master_nodes:
   	主结点数量的最少值 ,此值的公式为：(master_eligible_nodes / 2) + 1 ，比如：有3个符合要求的主结点，那么这里要设置为2。
   node.max_local_storage_nodes: 
   	单机允许的最大存储结点数，通常单机启动一个结点建议设置为1，开发环境如果单机启动多个节点可设置大于1。
   ```

   

   jvm.options

   设置最小及最大的jvm堆内存大小

   在jvm.options中设置, -Xms和-Xmx:

   - 两个值设置相等
   - 将Xmx设置为不超过物理内存的一半
   - 

   log4j2.properties

   日志文件配置, es使用log4j, 注意日志级别的配置

   

##### 4 启动es: bin/elasticsearch.bat. es的特点就是开箱即用, 无需配置, 启动即可. 

注意: es7 windows版本不支持机器学习, 所以elasticsearch.yml中添加如下几个参数:

```
node.name: node-1  
cluster.initial_master_nodes: ["node-1"]  
xpack.ml.enabled: false 
http.cors.enabled: true
http.cors.allow-origin: /.*/
```



5 检查es是否启动成功: 浏览器访问: http://localhost:9200/?Pretty

```
{
    "name": "node-1",
    "cluster_name": "elasticsearch",
    "cluster_uuid": "HqAKQ_0tQOOm8b6qU-2Qug",
    "version": {
        "number": "7.3.0",
        "build_flavor": "default",
        "build_type": "zip",
        "build_hash": "de777fa",
        "build_date": "2019-07-24T18:30:11.767338Z",
        "build_snapshot": false,
        "lucene_version": "8.1.0",
        "minimum_wire_compatibility_version": "6.8.0",
        "minimum_index_compatibility_version": "6.0.0-beta1"
    },
    "tagline": "You Know, for Search"
}
```

   解释：

​    name: node名称，取自机器的hostname

​    cluster_name: 集群名称（默认的集群名称就是elasticsearch）

​    version.number: 7.3.0，es版本号

​    version.lucene_version:封装的lucene版本号



##### 6 查看集群状态:  http://localhost:9200/_cluster/health

```
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 1,
    "number_of_data_nodes": 1,
    "active_primary_shards": 0,
    "active_shards": 0,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "task_max_waiting_in_queue_millis": 0,
    "active_shards_percent_as_number": 100
}
```

解释:

status: 集群状态. green所有分片可用. yellow所有主分片可用. red主分片不可用. 集群不可用



### 4.2 windows安装kibana

1. kibana是es数据的前端展示, 数据分析时, 可以方便地看到数据. 作为开发人员, 可以方便访问es. 
2. 下载, 解压kibana.
3. 启动kibana:  bin\kibana.bat.
4. 浏览器访问 http://localhost:5601进入dev Tools界面, 像plsql一样支持代码提示
5. 发送get请求, 查看集群状态 GET _cluster/health. 相当于浏览器访问

### 4.3 windows安装postman

### 4.4 windows安装head插件

head插件是es的一个可视化管理插件, 用来监视es的状态, 并通过head客户端和es服务交互, 比如创建映射, 创建索引等, head的项目地址在https://github.com/mobz/elasticsearch-head 。

从ES6.开始, head插件需要node.js运行

1. 安装node.js

2. 下载head并运行

   ```
   git clone git://github.com/mobz/elasticsearch-head.git 
   cd elasticsearch-head 
   npm install 
   npm run start 
   ```

3. 运行

   打开浏览器调试工具发现报错：

   Origin null is not allowed by Access-Control-Allow-Origin.

   原因是：head插件作为客户端要连接ES服务（localhost:9200），此时存在跨域问题，elasticsearch默认不允许跨域访问。

   设置elasticsearch允许跨域访问。在config/elasticsearch.yml 后面增加以下参数：

   ```
   #开启cors跨域访问支持，默认为false   
   http.cors.enabled: true   
   #跨域访问允许的域名地址，(允许所有域名)以上使用正则   
   http.cors.allow-origin: /.*/
   ```

   注意：将config/elasticsearch.yml另存为utf-8编码格式。

   成功连接ES

   注意：kibana\postman\head插件选择自己喜欢的一种使用即可。

   本教程使用kibana的dev tool，因为地址栏省略了http://localhost:9200。



## 5 es快速入门

### 5.1 文档(document)的数据格式

1. 应用系统的数据结构都是面向对象的, 具有复杂的数据结构

2. 对象的存储到数据库, 需要将关联的复杂对象属性插到另一张表, 查询的时候再拼接起来

3. es面向文档, 文档中存储的数据结构,与对象一直. 所以一个对象可以直接存成一个文档.

4. es的document用json数据格式来表达

   例如: 班级和学生的关系

   ```
   public class Student {
     private String id;
     private String name;
     
     private String classInfoId;  
   }
   
   private class ClassInfo {
     private String id;
     private String className;
   。。。。。
   
   }
   ```

   数据库中要设计所谓的一对多, 多对一的两张表, 外键等. 查询出来时, 还要关联, mybatis写映射文件, 很繁琐. 

   而在es中, 一个学生存在文档如下: 

   ```
   {
       "id":"1",
       "name": "张三",
       "last_name": "zhang",
       "classInfo": {
           "id": "1",
           "className": "三年二班",     
       }
   }
   ```

   ### 5.2 图书网站商品管理案例: 背景介绍

   有一个售卖图书的网站, 需要为其基于es构建一个后台系统, 提供以下功能:

   1. 对商品信息进行crud(增删改查)操作
   2. 执行简单的结构化查询
   3. 可以执行简单的全文检索,以及复杂的phrase(短语)检索
   4. 对于全文检索的结果, 可以进行高亮显示
   5. 对数据进行简单的聚合分析

   ### 5.3 简单的集群管理

   #### 5.3.1 快速检查集群的健康状况

   es提供了一套api, 叫做cat api, 可以查看es中各种各样的数据

   GET /_cat/health?v

   ```
   epoch      timestamp cluster       status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
   1568635460 12:04:20  elasticsearch green           1         1      4   4    0    0        0             0                  -                100.0%
   ```

   如何快速了解集群的健康状况? green, yellow, red.- 

   - green: 每个索引的primary shard和replica shard都是active状态
   - yellow: 每个索引的primary shard都是active状态的, 但是部分replica shard不是active状态, 处于不可用的状态
   - red: 不是所有索引的priamry shard都是active状态的, 部分索引数据丢失. 

   #### 5.3.2 快速查看集群中有哪些索引

   GET /_cat/indices?v

   ```
   health status index                           uuid                   pri rep docs.count docs.deleted store.size pri.store.size
   green  open   .kibana_task_manager            JBMgpucOSzenstLcjA_G4A   1   0          2            0     45.5kb         45.5kb
   green  open   .monitoring-kibana-7-2019.09.16 LIskf15DTcS70n4Q6t2bTA   1   0        433            0    218.2kb        218.2kb
   green  open   .monitoring-es-7-2019.09.16     RMeUN3tQRjqM8xBgw7Zong   1   0       3470         1724      1.9mb          1.9mb
   green  open   .kibana_1                       1cRiyIdATya5xS6qK5pGJw   1   0          4            0     18.2kb         18.2kb
   ```
   #### 5.3.3 简单的索引操作
   
   - 创建索引 PUT /demo_index?pretty
   
     ```
     {
       "acknowledged" : true,
       "shards_acknowledged" : true,
       "index" : "demo_index"
     }
     ```
   
      
   
   - 删除索引  DELETE /demo_index?pretty



### 5.4 商品的CRUD操作

#### 5.4.1 新建图书索引

首先建立图书索引 book

语法: put /index

PUT /book

#### 5.4.2 新增图书: 新增文档

语法: PUT /index/type/id

```
PUT /book/_doc/1

{
"name": "Bootstrap开发",
"description": "Bootstrap是由Twitter推出的一个前台页面开发css框架，是一个非常流行的开发框架，此框架集成了多种页面效果。此开发框架包含了大量的CSS、JS程序代码，可以帮助开发者（尤其是不擅长css页面开发的程序人员）轻松的实现一个css，不受浏览器限制的精美界面css效果。",
"studymodel": "201002",
"price":38.6,
"timestamp":"2019-08-25 19:11:35",
"pic":"group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
"tags": [ "bootstrap", "dev"]
}
```

```
PUT /book/_doc/2
{
"name": "java编程思想",
"description": "java语言是世界第一编程语言，在软件开发领域使用人数最多。",
"studymodel": "201001",
"price":68.6,
"timestamp":"2019-08-25 19:11:35",
"pic":"group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
"tags": [ "java", "dev"]
}
```

```
PUT /book/_doc/3
{
"name": "spring开发基础",
"description": "spring 在java领域非常流行，java程序员都在用。",
"studymodel": "201001",
"price":88.6,
"timestamp":"2019-08-24 19:11:35",
"pic":"group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
"tags": [ "spring", "java"]
}
```

结果

```
{
  "_index" : "book",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```



#### 5.4.3 查询图书: 检索文档

语法: GET /index/type/id

查看图书 GET /book/_doc/1就可看到json形式的文档, 方便程序解析.

```
{
  "_index" : "book",
  "_type" : "_doc",

  "_id" : "1",

  "_version" : 4,

  "_seq_no" : 5,

  "_primary_term" : 1,

  "found" : true,

  "_source" : {

    "name" : "Bootstrap开发",

    "description" : "Bootstrap是由Twitter推出的一个前台页面开发css框架，是一个非常流行的开发框架，此框架集成了多种页面效果。此开发框架包含了大量的CSS、JS程序代码，可以帮助开发者（尤其是不擅长css页面开发的程序人员）轻松的实现一个css，不受浏览器限制的精美界面css效果。",

    "studymodel" : "201002",

    "price" : 38.6,

    "timestamp" : "2019-08-25 19:11:35",

    "pic" : "group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",

    "tags" : [

      "bootstrap",

      "开发"

    ]

  }

}
```



为了方便查看索引中的数据, kibana可以如下操作

kibana-discover-create index pattern-index pattern填写book

下一步, 再点击discover就可看到数据.

点击json还可以看到原始数据

为了方便查看索引中的数据, head可以如下操作

点击数据浏览, 点击book索引

#### 5.4.4 修改图书: 替换操作

```
PUT /book/_doc/1
{
    "name": "Bootstrap开发教程1",
    "description": "Bootstrap是由Twitter推出的一个前台页面开发css框架，是一个非常流行的开发框架，此框架集成了多种页面效果。此开发框架包含了大量的CSS、JS程序代码，可以帮助开发者（尤其是不擅长css页面开发的程序人员）轻松的实现一个css，不受浏览器限制的精美界面css效果。",
    "studymodel": "201002",
    "price":38.6,
    "timestamp":"2019-08-25 19:11:35",
    "pic":"group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
    "tags": [ "bootstrap", "开发"]
}
```

替换操作是整体覆盖, 要带上所有信息. 

##### 5.4.5 修改图书, 更新文档

语法: POST /index/type/id/_update

或者 POST /index/_update/id

```
POST /book/_update/1/ 
{
  "doc": {
   "name": " Bootstrap开发教程高级"
  }
}
```

返回

```
{
  "_index" : "book",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 10,
  "result" : "updated",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 11,
  "_primary_term" : 1
}
```



#### 5.4.6 删除图书 删除文档

语法: DELETE /index/_doc/1

返回:

```
{
  "_index" : "book",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 11,
  "result" : "deleted",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 12,
  "_primary_term" : 1

}
```



## 6 文档document入门

### 6.1 默认自带字段解析

```
{
  "_index" : "book",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 10,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "name" : "Bootstrap开发教程1",
    "description" : "Bootstrap是由Twitter推出的一个前台页面开发css框架，是一个非常流行的开发框架，此框架集成了多种页面效果。此开发框架包含了大量的CSS、JS程序代码，可以帮助开发者（尤其是不擅长css页面开发的程序人员）轻松的实现一个css，不受浏览器限制的精美界面css效果。",
    "studymodel" : "201002",
    "price" : 38.6,
    "timestamp" : "2019-08-25 19:11:35",
    "pic" : "group1/M00/00/00/wKhlQFs6RCeAY0pHAAJx5ZjNDEM428.jpg",
    "tags" : [
      "bootstrap",
      "开发"
    ]
  }
}
```

#### 6.1.1 _index

- 含义: 此文档属于哪个索引
- 原则: 类似数据放在一个索引中. 数据库中表的定义规则. 如图书信息放在book索引中, 员工信息放在employee索引中. 各个索引存储和搜索时互不影响
- 定义规则: 英文小写, 尽量不要用特殊字符. 比如order, user

#### 6.1.2 _type

- 含义: 类别. book, java, node
- 注意: 以后的es9将彻底删除此字段, 所以当前版本在不断弱化type. 不需要关注, 见到_type都为doc. 

#### 6.1.3 _id

- 含义: 文档的唯一标志. 就像表的id主键. 结合索引可以标识和定义一个文档
- 生成: 手动(put /index/_doc/id), 自动

#### 6.1.4 创建索引时, 不同数据放到不同索引中



### 6.2 生成文档id

#### 6.2.1 手动生成id

场景: 数据从其他系统导入时, 本身有唯一主键. 如数据库中的图书, 员工信息等

用法: put /index/_doc/id

```
PUT /test_index/_doc/1
{
  "test_field": "test"
}
```



#### 6.2.2 自动生成id

用法: POST /index/_doc

```
POST /test_index/_doc
{
  "test_field": "test1"
}
```

返回：

```
{
  "_index" : "test_index",
  "_type" : "_doc",
  "_id" : "x29LOm0BPsY0gSJFYZAl",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

自动id特点: 

长度为20个字符, URL安全, base64编码, GUID, 分布式生成不冲突. 

### 6.3 _source字段

#### 6.3.1 _source

- 含义: 插入数据时的所有字段和值. 在get获取数据时, 在_source字段中原样返回. 

  GET /book/_doc/1

#### 6.3.2 定制返回字段

就像sql不要select *, 而要指定字段一样. GET /book/_doc/1?_source_includes=name,price

```
{
  "_index" : "book",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "_seq_no" : 10,
  "_primary_term" : 1,
  "found" : true,
  "_source" : {
    "price" : 38.6,
    "name" : "Bootstrap开发教程1"
  }
}
```



### 6.4 文档的替换与删除

#### 6.4.1 全量替换

执行两次, 返回结果中版本号(_version)在不断上升. 此过程为全量替换. 

```
PUT /test_index/_doc/1
{
  "test_field": "test"
}
```

实质: 旧文档的内容不会立即删除, 只是标记为deleted. 适当的时机, 集群会将这些文档删除. 

#### 6.4.2 强制创建

为防止覆盖原有数据, 我们在新增时, 设置为强制创建, 不会覆盖原有文档. 

语法 PUT /index/_doc/id/create

```
PUT /test_index/_doc/1/_create
{
  "test_field": "test"
}
```

返回:

```
{
  "error": {
    "root_cause": [
      {
        "type": "version_conflict_engine_exception",
        "reason": "[2]: version conflict, document already exists (current version [1])",
        "index_uuid": "lqzVqxZLQuCnd6LYtZsMkg",
        "shard": "0",
        "index": "test_index"
      }
    ],
    "type": "version_conflict_engine_exception",
    "reason": "[2]: version conflict, document already exists (current version [1])",
    "index_uuid": "lqzVqxZLQuCnd6LYtZsMkg",
    "shard": "0",
    "index": "test_index"
  },
  "status": 409
}
```



#### 6.4.3 删除

DELETE /index/_doc_id

实质: 旧文档那个的内容不会立即删除, 只是标记为deleted. 适当的时机, 集群会将这些文档删除. 

lazy delete

### 6.5 局部替换 partial update

使用 PUT /index/type/id为文档全量替换, 需要将文档所欲数据提交

partial update局部替换则只修改变动字段

用法:

```
post /index/type/id/_update 
{
   "doc": {
      "field"："value"
   }
}
```

#### 图解内部原理

内部与全量替换是一样的, 就文档标记为删除, 新建一个文档. 

优点:

- 大大减少网络传输次数和流量, 提升性能
- 减少并发冲突发生的概率

#### 演示

插入文档

```
PUT /test_index/_doc/5
{
  "test_field1": "itcst",
  "test_field2": "itheima"
}
```

修改字段1

```
POST /test_index/_doc/5/_update
{
  "doc": {
    "test_field2": " itheima 2"
  }
}
```



### 6.6 使用脚本更新

es可以内置脚本执行复杂操作. 例如painless脚本

注意: groovy脚本在es6以后就不支持了. 原因是耗内存, 不安全远程注入漏洞. 

### 6.7 图解es的并发问题

如同秒杀, 多线程情况下, es同样会出现并发冲突问题

### 6.8 图解悲观锁与乐观锁机制

为控制并发问题, 我们通常采用锁机制. 分为悲观锁和乐观锁两种机制. 

悲观锁: 很悲观, 所有的情况都上锁. 此时只有一个线程可以操作数据. 具体例子为数据库中的行级锁, 表级锁, 读锁, 写锁等. 

特点: 优点是方便, 直接加锁, 对程序透明. 缺点是效率低. 

乐观锁: 很乐观, 对数据本身不加锁. 提交数据时, 通过一种机制验证是否存在冲突, 如es中通过版本号验证. 

特点: 优点是并发能力高. 缺点是操作繁琐, 在提交数据时, 可能反复重试多次

### 6.9 图解es内部基于_version乐观锁控制

实验基于_version的版本控制

es对于文档的增删改都是基于版本号

1. 新增多次文档

   ```
   PUT /test_index/_doc/3
   {
     "test_field": "test"
   }
   ```

   返回版本号递增

2. 删除此文档

   ```
   DELETE /test_index/_doc/3
   ```

   返回:

   ```
   DELETE /test_index/_doc/3
   {
     "_index" : "test_index",
     "_type" : "_doc",
     "_id" : "2",
     "_version" : 6,
     "result" : "deleted",
     "_shards" : {
       "total" : 2,
       "successful" : 1,
       "failed" : 0
     },
     "_seq_no" : 7,
     "_primary_term" : 1
   }
   ```

3. 再新增

   ```
   PUT /test_index/_doc/3
   {
     "test_field": "test"
   }
   ```

   可以看到版本号依次递增, 验证延迟删除策略. 

   如果删除一条数据立马删除的话, 所有分片和副本都要立马删除, 对es集群压力太大. 

#### 图解es内部并发控制

es内部主从同步时, 是多线程异步. 乐观锁机制. 



### 6.10 演示客户端程序基于_version并发操作流程

java python客户端更新的机制. 

### 新建文档

```
PUT /test_index/_doc/5
{
  "test_field": "itcast"
}
```

返回:

```
{
  "_index" : "test_index",
  "_type" : "_doc",
  "_id" : "3",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 8,
  "_primary_term" : 1
}
```

#### 客户端1修改. 带版本号1. 

首先获取数据的当前版本号

```
GET /test_index/_doc/5
```

更新文档

```
PUT /test_index/_doc/5?version=1
{
  "test_field": "itcast1"
}
PUT /test_index/_doc/5?if_seq_no=21&if_primary_term=1
{
  "test_field": "itcast1"
}
```

#### 客户端2并发修改. 带版本号1. 

```
PUT /test_index/_doc/5?version=1
{
  "test_field": "itcast2"
}
PUT /test_index/_doc/5?if_seq_no=21&if_primary_term=1
{
  "test_field": "itcast1"
}
```

报错

#### 客户端2重新查询. 得到最新版本为2. seq_no=22

```
GET /test_index/_doc/4
```

#### 客户端2并发修改. 带版本号2.

```
PUT /test_index/_doc/4?version=2
{
  "test_field": "itcast2"
}
es7
PUT /test_index/_doc/5?if_seq_no=22&if_primary_term=1
{
  "test_field": "itcast2"
}
```

修改成功. 



### 6.11 演示自己手动控制版本号external version

背景: 已有数据是在数据库中, 有自己手动维护的版本号的情况下, 可以使用external version控制. hbase

要求: 修改时extenal version要大于当前文档的_version

对比: 基于_version时, 修改的文档version等于当前文档的版本号.. 

使用?version=1&version_type=external

#### 新建文档

```
PUT /test_index/_doc/4
{
  "test_field": "itcast"
}
```

更新文档:

#### 客户端1修改文档

```
PUT /test_index/_doc/4?version=2&version_type=external
{
  "test_field": "itcast1"
}
```

#### 客户端2同时修改

```
PUT /test_index/_doc/4?version=2&version_type=external
{
  "test_field": "itcast2"
}
```

返回: 

```
{
  "error": {
    "root_cause": [
      {
        "type": "version_conflict_engine_exception",
        "reason": "[4]: version conflict, current version [2] is higher or equal to the one provided [2]",
        "index_uuid": "-rqYZ2EcSPqL6pu8Gi35jw",
        "shard": "1",
        "index": "test_index"
      }
    ],
    "type": "version_conflict_engine_exception",
    "reason": "[4]: version conflict, current version [2] is higher or equal to the one provided [2]",
    "index_uuid": "-rqYZ2EcSPqL6pu8Gi35jw",
    "shard": "1",
    "index": "test_index"
  },
  "status": 409
}
```

#### 客户端2重新查询数据

```
GET /test_index/_doc/4
```

#### 客户端2重新修改数据

```
PUT /test_index/_doc/4?version=3&version_type=external
{
  "test_field": "itcast2"
}
```



### 6.12 更新时retry_on_conflict参数

retry_on_conflict

指定重试次数

```
POST /test_index/_doc/5/_update?retry_on_conflict=3
{
  "doc": {
    "test_field": "itcast1"
  }
}
```

#### 与_version结合使用

```
POST /test_index/_doc/5/_update?retry_on_conflict=3&version=22&version_type=external
{
  "doc": {
    "test_field": "itcast1"
  }
}
```



### 6.13 批量查询mget

单条查询 GET /test_inde/_doc/1, 如果查询多个id的文档一条一条查询, 网络开销太大. 

#### mget批量查询

```
GET /_mget
{
   "docs" : [
      {
         "_index" : "test_index",
         "_type" :  "_doc",
         "_id" :    1
      },
      {
         "_index" : "test_index",
         "_type" :  "_doc",
         "_id" :    7
      }
   ]
}
```

返回: 

```
{
  "docs" : [
    {
      "_index" : "test_index",
      "_type" : "_doc",
      "_id" : "2",
      "_version" : 6,
      "_seq_no" : 12,
      "_primary_term" : 1,
      "found" : true,
      "_source" : {
        "test_field" : "test12333123321321"
      }
    },
    {
      "_index" : "test_index",
      "_type" : "_doc",
      "_id" : "3",
      "_version" : 6,
      "_seq_no" : 18,
      "_primary_term" : 1,
      "found" : true,
      "_source" : {
        "test_field" : "test3213"
      }
    }
  ]
}
```

去掉type

```
GET /_mget
{
   "docs" : [
      {
         "_index" : "test_index",
         "_id" :    2
      },
      {
         "_index" : "test_index",
         "_id" :    3
      }
   ]
}
```

#### 同一索引下批量查询

```
GET /test_index/_mget
{
   "docs" : [
      {
         "_id" :    2
      },
      {
         "_id" :    3
      }
   ]
}
```

#### 第三种写法: 搜索写法

```
post /test_index/_doc/_search
{
    "query": {
        "ids" : {
            "values" : ["1", "7"]
        }
    }
}
```



### 6.14 批量增删改bulk

bulk操作解释将文档的增删改查一些列操作, 通过一次请求全都做完. 减少网络传输次数. 

语法: 

```
POST /_bulk
{"action": {"metadata"}}
{"data"}
```

如下操作, 删除5, 新增14, 修改2.

```
POST /_bulk
{ "delete": { "_index": "test_index",  "_id": "5" }} 
{ "create": { "_index": "test_index",  "_id": "14" }}
{ "test_field": "test14" }
{ "update": { "_index": "test_index",  "_id": "2"} }
{ "doc" : {"test_field" : "bulk test"} }
```

总结: 

1. 功能
   - delete: 删除一个文档, 只要1个json串就可以了. 
   - create: 相当于强制创建 PUT /index/type/id/_create
   - index: 普通的put操作, 可以是创建文档, 也可以是全量替换文档
   - update: 执行的是局部更新partial update操作. 
2. 格式 每个json不能换行. 相邻json必须换行
3. 隔离: 每个操作互不影响. 操作失败的行会返回失败信息
4. 实际用法: bulk请求一次不要太大, 否则一下挤压到内存中, 性能会下降. 所以, 一次请求几千个操作, 大小在几M正好. 

### 6.15 文档概念学习总结

**章节回顾**

- 文档的增删改查
- 文档字段解析
- 内部锁机制
- 批量查询修改

**es是什么**

一个分布式的文档数据存储系统(distributed document store). es看做一个分布式nosql数据库. 如redis, mongodb, hbase. 

文档数据: es可以存储和操作json文档类型的数据, 而且这也是es的核心数据结构. 

存储系统: es可以对json文档类型的数据进行存储, 查询, 创建, 更新, 删除等操作. 

**应用场景**

- 大数据. es的分布式特点, 水平扩容承载大数据
- 数据结构灵活. 列随时变化. 使用关系型数据库将会建立大量的关联表, 增加系统复杂性. 
- 数据操作简单. 就是查询, 不涉及事务. 

**举例**

电商页面, 传统论坛页面等, 面向的对象比较复杂, 但是作为终端, 没有太复杂的功能(事务), 只涉及简单的增删改查crud. 

这个时候选用es这种nosql型的数据存储, 比传统的复杂的事务强大的关系型数据库, 更加合适一些. 无论是性能, 还是吞吐量, 可能都会更好. 



# 7 java api实现文档管理

## 7.1 es技术特点

- es技术比较特殊, 不像其他分布式, 大数据课程, Hadoop, spark, HBASE. es代码层面很好写, 难的是概念的理解. 
- es最重要的是他的rest api. 跨语言的. 在真实生产中, 探查数据, 分析数据, 使用rest更方便. 
- 本课程将会大量讲解内部原理及rest api. java代码会在重要的api后面学习. 

## 7.2 java 客户端简单获取数据

java api文档: https://www.elastic.co/guide/en/elasticsearch/client/java-rest/7.3/java-rest-overview.html

low: 偏向底层.

high: 高级封装, 足够. 

### 1 导包

``` xml
    <dependency>
        <groupId>org.elasticsearch.client</groupId>
        <artifactId>elasticsearch-rest-high-level-client</artifactId>
        <version>7.3.0</version>
        <exclusions>
            <exclusion>
                <groupId>org.elasticsearch</groupId>
                <artifactId>elasticsearch</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.elasticsearch</groupId>
        <artifactId>elasticsearch</artifactId>
        <version>7.3.0</version>
    </dependency>
```

### 2 代码

步骤:

- 获取连接客户端
- 构建请求
- 执行
- 获取结果

```java
    //获取连接客户端
    RestHighLevelClient client = new RestHighLevelClient(
            RestClient.builder(
                    new HttpHost("localhost", 9200, "http")));
    //构建请求
    GetRequest getRequest = new GetRequest("book", "1");
    // 执行
    GetResponse getResponse = client.get(getRequest, RequestOptions.DEFAULT);
    // 获取结果
    if (getResponse.isExists()) {
        long version = getResponse.getVersion();
        String sourceAsString = getResponse.getSourceAsString();//检索文档(String形式)
        System.out.println(sourceAsString);
    }
```



## 7.3 结合spring-boot-test测试文档查询

### 0 为什么使用spring boot test

- 当今趋势
- 方便开发
- 创建连接交由spring容器, 避免每次请求的网络开销

### 1 导包

```  xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <version>2.0.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <version>2.0.6.RELEASE</version>
        </dependency>
```

### 2 配置application.yml

```yaml
spring:
  application:
    name: service-search
heima:
  elasticsearch:
    hostlist: 127.0.0.1:9200 #多个结点中间用逗号分隔
```

### 3 代码

- 主类
- 配置类
- 测试类

```
@SpringBootTest
@RunWith(SpringRunner.class)
//查询文档
       @Test
    public void testGet() throws IOException {
        //构建请求
        GetRequest getRequest = new GetRequest("test_post", "1");

        //========================可选参数 start======================
        //为特定字段配置_source_include
//        String[] includes = new String[]{"user", "message"};
//        String[] excludes = Strings.EMPTY_ARRAY;
//        FetchSourceContext fetchSourceContext = new FetchSourceContext(true, includes, excludes);
//        getRequest.fetchSourceContext(fetchSourceContext);

        //为特定字段配置_source_excludes
//        String[] includes1 = new String[]{"user", "message"};
//        String[] excludes1 = Strings.EMPTY_ARRAY;
//        FetchSourceContext fetchSourceContext1 = new FetchSourceContext(true, includes1, excludes1);
//        getRequest.fetchSourceContext(fetchSourceContext1);

        //设置路由
//        getRequest.routing("routing");

        // ========================可选参数 end=====================


        //查询 同步查询
      GetResponse getResponse = client.get(getRequest, RequestOptions.DEFAULT);

        //异步查询
//        ActionListener<GetResponse> listener = new ActionListener<GetResponse>() {
//            //查询成功时的立马执行的方法
//            @Override
//            public void onResponse(GetResponse getResponse) {
//                long version = getResponse.getVersion();
//                String sourceAsString = getResponse.getSourceAsString();//检索文档(String形式)
//                System.out.println(sourceAsString);
//            }
//
//            //查询失败时的立马执行的方法
//            @Override
//            public void onFailure(Exception e) {
//                e.printStackTrace();
//            }
//        };
//        //执行异步请求
//        client.getAsync(getRequest, RequestOptions.DEFAULT, listener);
//        try {
//            Thread.sleep(5000);
//        } catch (InterruptedException e) {
//            e.printStackTrace();
//        }

        // 获取结果
        if (getResponse.isExists()) {
            long version = getResponse.getVersion();

            String sourceAsString = getResponse.getSourceAsString();//检索文档(String形式)
            System.out.println(sourceAsString);
            byte[] sourceAsBytes = getResponse.getSourceAsBytes();//以字节接受
            Map<String, Object> sourceAsMap = getResponse.getSourceAsMap();
            System.out.println(sourceAsMap);

        }else {

        }

    }
```



## 7.4 结合spring-boot-test测试文档新增

**rest api**

```
PUT test_post/_doc/2
{
  "user":"tomas",
  "postDate":"2019-07-18",
  "message":"trying out es1"
}
```

**代码**

```java
@Test
    public void testAdd() throws IOException {
//        1构建请求
        IndexRequest request=new IndexRequest("test_posts");
        request.id("3");
//        =======================构建文档============================
//        构建方法1
        String jsonString="{\n" +
                "  \"user\":\"tomas J\",\n" +
                "  \"postDate\":\"2019-07-18\",\n" +
                "  \"message\":\"trying out es3\"\n" +
                "}";
        request.source(jsonString, XContentType.JSON);

//        构建方法2
//        Map<String,Object> jsonMap=new HashMap<>();
//        jsonMap.put("user", "tomas");
//        jsonMap.put("postDate", "2019-07-18");
//        jsonMap.put("message", "trying out es2");
//        request.source(jsonMap);

//        构建方法3
//        XContentBuilder builder= XContentFactory.jsonBuilder();
//        builder.startObject();
//        {
//            builder.field("user", "tomas");
//            builder.timeField("postDate", new Date());
//            builder.field("message", "trying out es2");
//        }
//        builder.endObject();
//        request.source(builder);
//        构建方法4
//        request.source("user","tomas",
//                    "postDate",new Date(),
//                "message","trying out es2");
//
//        ========================可选参数===================================
        //设置超时时间
        request.timeout(TimeValue.timeValueSeconds(1));
        request.timeout("1s");

        //自己维护版本号
//        request.version(2);
//        request.versionType(VersionType.EXTERNAL);



//        2执行
        //同步
        IndexResponse indexResponse = client.index(request, RequestOptions.DEFAULT);
        //异步
//        ActionListener<IndexResponse> listener=new ActionListener<IndexResponse>() {
//            @Override
//            public void onResponse(IndexResponse indexResponse) {
//
//            }
//
//            @Override
//            public void onFailure(Exception e) {
//
//            }
//        };
//        client.indexAsync(request,RequestOptions.DEFAULT, listener );
//        try {
//            Thread.sleep(5000);
//        } catch (InterruptedException e) {
//            e.printStackTrace();
//        }


//        3获取结果
        String index = indexResponse.getIndex();
        String id = indexResponse.getId();
        //获取插入的类型
        if(indexResponse.getResult()== DocWriteResponse.Result.CREATED){
            DocWriteResponse.Result result=indexResponse.getResult();
            System.out.println("CREATED:"+result);
        }else if(indexResponse.getResult()== DocWriteResponse.Result.UPDATED){
            DocWriteResponse.Result result=indexResponse.getResult();
            System.out.println("UPDATED:"+result);
        }

        ReplicationResponse.ShardInfo shardInfo = indexResponse.getShardInfo();
        if(shardInfo.getTotal()!=shardInfo.getSuccessful()){
            System.out.println("处理成功的分片数少于总分片！");
        }
        if(shardInfo.getFailed()>0){
           for (ReplicationResponse.ShardInfo.Failure failure:shardInfo.getFailures()) {
               String reason = failure.reason();//处理潜在的失败原因
               System.out.println(reason);
           }
        }
    }
```

## 7.5 结合spring-boot-test测试文档修改

**rest api**

```
post /test_posts/_doc/3/_update 
{
   "doc": {
      "user"："tomas J"
   }
}
```

**代码**

```java
 @Test
    public void testUpdate() throws IOException {
//        1构建请求
        UpdateRequest request = new UpdateRequest("test_posts", "3");
        Map<String, Object> jsonMap = new HashMap<>();
        jsonMap.put("user", "tomas JJ");
        request.doc(jsonMap);
//===============================可选参数==========================================
        request.timeout("1s");//超时时间

        //重试次数
        request.retryOnConflict(3);

        //设置在继续更新之前，必须激活的分片数
//        request.waitForActiveShards(2);
        //所有分片都是active状态，才更新
//        request.waitForActiveShards(ActiveShardCount.ALL);

//        2执行
//        同步
        UpdateResponse updateResponse = client.update(request, RequestOptions.DEFAULT);
//        异步

//        3获取数据
        updateResponse.getId();
        updateResponse.getIndex();

        //判断结果
        if (updateResponse.getResult() == DocWriteResponse.Result.CREATED) {
            DocWriteResponse.Result result = updateResponse.getResult();
            System.out.println("CREATED:" + result);
        } else if (updateResponse.getResult() == DocWriteResponse.Result.UPDATED) {
            DocWriteResponse.Result result = updateResponse.getResult();
            System.out.println("UPDATED:" + result);
        }else if(updateResponse.getResult() == DocWriteResponse.Result.DELETED){
            DocWriteResponse.Result result = updateResponse.getResult();
            System.out.println("DELETED:" + result);
        }else if (updateResponse.getResult() == DocWriteResponse.Result.NOOP){
            //没有操作
            DocWriteResponse.Result result = updateResponse.getResult();
            System.out.println("NOOP:" + result);
        }
    }
```

## 7.6 结合spring-boot-test测试文档删除

**rest api**

```
DELETE /test_posts/_doc/3
```

**代码**

```java
 @Test
    public void testDelete() throws IOException {
//        1构建请求
        DeleteRequest request =new DeleteRequest("test_posts","3");
        //可选参数


//        2执行
        DeleteResponse deleteResponse = client.delete(request, RequestOptions.DEFAULT);


//        3获取数据
        deleteResponse.getId();
        deleteResponse.getIndex();

        DocWriteResponse.Result result = deleteResponse.getResult();
        System.out.println(result);
}
```



## 7.7 结合spring-boot-test测试文档bulk

**rest api**

```
POST /_bulk
{"action": {"metadata"}}
{"data"}
```

**代码**

```
@Test
    public void testBulk() throws IOException {
//        1创建请求
        BulkRequest request = new BulkRequest();
//        request.add(new IndexRequest("post").id("1").source(XContentType.JSON, "field", "1"));
//        request.add(new IndexRequest("post").id("2").source(XContentType.JSON, "field", "2"));

        request.add(new UpdateRequest("post","2").doc(XContentType.JSON, "field", "3"));
        request.add(new DeleteRequest("post").id("1"));

//        2执行
        BulkResponse bulkResponse = client.bulk(request, RequestOptions.DEFAULT);

        for (BulkItemResponse itemResponse : bulkResponse) {
            DocWriteResponse itemResponseResponse = itemResponse.getResponse();

            switch (itemResponse.getOpType()) {
                case INDEX:
                case CREATE:
                    IndexResponse indexResponse = (IndexResponse) itemResponseResponse;
                    indexResponse.getId();
                    System.out.println(indexResponse.getResult());
                    break;
                case UPDATE:
                    UpdateResponse updateResponse = (UpdateResponse) itemResponseResponse;
                    updateResponse.getIndex();
                    System.out.println(updateResponse.getResult());
                    break;
                case DELETE:
                    DeleteResponse deleteResponse = (DeleteResponse) itemResponseResponse;
                    System.out.println(deleteResponse.getResult());
                    break;
            }
        }
    }
```



# 8 图解es内部机制

## 8.1 图解es分布式基础

### 8.1.1 es对复杂分布式机制的透明隐藏特性

- 分布式机制: 分布式数据存储集共享
- 分片机制: 数据存储到哪个分片, 副本数据写入. 
- 集群发现机制: cluster discovery. 新启动es实例, 自动加入集群. 
- shard负载均衡: 大量数据写入集查询, es会将数据平均分配. 
- shard副本: 新增副本数, 分片重分配. 

### 8.1.2 es的垂直扩容与水平扩容

垂直扩容: 使用更加强大的服务器替代老服务器. 但单机存储及运算能力有上限. 且成本直线上升. 如10t服务器1万, 单个10t服务器可能20万.

水平扩容: 采购更多服务器, 加入集群, 大数据. 

### 8.1.3  增加或者减少节点时的数据rebalance

新增或者减少es实例时, es集群会将数据重新分配

### 8.1.4 master节点

功能: 

- 创建/删除节点
- 创建/删除索引

### 8.1.5 节点对等的分布式架构

- 节点对等, 每个节点都能接收所有的请求
- 自动请求路由
- 响应收集

## 8.2 图解分片shard, 副本replica机制

### 8.2.1 shard&replica机制

- 每个index包含一个或多个shard
- 每个shard都是一个最小工作单元, 承载部分数据, lucene实例, 完整的建立索引和处理请求的能力
- 增删节点时, shard会自动在nodes中负载均衡
- primaary shard和replica shard, 每个document肯定只存在于某一个primary shard以及其对应的replica shard中, 不可能存在多个primary shard
- replica shard是primary shard的副本, 负责容错, 以及承担读请求负载
- primary shard的数量在创建索引的时候就固定了 replica shard 的数量可以随时修改. 
- primary shard的默认数量是1, replica默认的是1, 默认共有2个shard, 1个primary shard, 1个replica shard(es7以前primary shard的默认是5, replica 默认是1, 默认有10个shard, 5个primary shard, 5个replica shard)
- primary shard不能和自己的replica shard放在同一个节点上(否则节点宕机, primary shard和副本都丢失, 起不到容错的作用), 但是可以和其他primary shard的replica shard放在同一个节点上. 

## 8.3 图解单node环境下创建index是什么样子的

- 单node环境下, 创建一个index, 有3个primary shard, 3个replica shard
- 集群status是yellow
- 这个时候, 只会将3个primary shard分配到仅有的一个node上去, 另外3个replica shard是无法分配的
- 集群可以正常工作, 但是一旦节点出现宕机, 数据全部丢失, 而且集群不可用, 无法承接任何请求

```
PUT /test_index1
{
   "settings" : {
      "number_of_shards" : 3,
      "number_of_replicas" : 1
   }
}
```

## 8.4 图解2个node环境下replica shard 是如何分配的

- replica shard分配: 3个primary shard, 3个replica shard, 1个node
- primary -> replica 同步
- 读请求: primary/replica

## 8.5 图解横向扩容

- 分片自动负载均衡, 分片向空闲机器转移
- 每个节点存储更少分片, 系统资源给与每个分片的资源更多, 整体集群性能提高
- 扩容极限: 节点数大于整体分片数, 则必有空闲机器
- 超出扩容极限时, 可以增加副本数, 如设置副本数为2,总共3*3=9个分片, 9台机器同时运行, 存储和搜索性能更强. 容错性更好.
- 容错性: 只要一个索引的所有主分片在, 集群就可以运行. 

## 8.6 图解es容错机制 master选举, replica容错, 数据恢复

以3分片, 2副本数, 3节点为例介绍:

- master node宕机, 自动master选举, 集群为red
- replica容错: 新master将replica提升为primary shard, yellow
- 重启宕机node: master copy replica到该node, 使用原有的shard并同步宕机后的修改, green



# 9 图解文档存储机制

## 9.1 数据路由

### 9.1.1 文档存储如何路由到相应分片

一个文档, 最终会落在主分片的一个分片上, 到底应该落在哪一个分片上, 这是数据路由. 

### 9.1.2 路由算法

```
shard = hash(routing)%number_of_primary_shards
```

哈希值对主分片数取模

举例: 

对一个文档进行curd, 都会带一个路由值routing number. 默认为文档_id(可能是手动指定, 也可能是自动生成). 

存储1号文档, 经过哈希计算, 哈希值为2, 此索引有3个主分片, 那么计算2%3=2, 就计算出此文档在P2分片上. 

决定一个document在哪个shard上, 最重要的一个值就是routing值, 默认是_id, 也可以手动指定, 相同的routing值, 每次进来, 从hash函数中, 产出一个hash值一定是相同的. 

无论hash值是几, 无论是什么数字, 对number_of_primary_shards求余数, 结果一定是在0~number_of_primary_shards-1之间的范围. 0, 1, 2. 

### 9.1.3 手动指定routing number

```
PUT /test_index/_doc/15?routing=num
{
  "num": 0,
  "tags": []
}
```

场景: 在程序中, 架构师可以手动指定已有数据的一个属性为路由值, 好处是可以定制一类文档数据存储到一个分片中. 缺点是设计不好, 会造成数据倾斜. 

所以, 不同文档尽量放到不同的索引中. 剩下的事情交给es集群自己处理. 

### 9.1.4 主分片数量不可变

涉及到以往数据的查询搜索, 所以一旦建立索引, 主分片不可变. 

## 9.2 图解文档的增删改查内部机制

增删改查可以看做update, 都是对数据的改动. 一个改动请求发送到es集群, 经理一下四个步骤: 

- 客户端选择一个node发送请求过去, 这个node就是coordinating node(协调节点)
- coordinating node, 对document进行路由, 将请求转发给对应的node(有primary shard)
- 实际的node上的primary shard处理请求, 然后将数据同步到replica node
- coordinate node, 如果发现primary node和所有replica node都搞定之后, 就反贿赂响应结果给客户端

## 9.3 图解文档的查询内部机制

- 客户端发送请求到任意一个node, 称为coordinate node.
- coordinate node对document进行路由, 将请求转发到对应的node, 此时会使用round-ribon随机轮询算法, 在primary shard以及其所有replica中随机选择一个, 让读请求负载均衡
- 接收请求的node返回document给coordinate
- coordinate返回document给客户端
- 特殊情况: document如果还在建立索引过程中, 可能只有primary shard有, 任何一个replica shard都没有, 此时可能会导致无法读取到document, 但是document完成索引建立之后, primary shard和replica shard就都有了. 

## 9.4 bulk api奇特的json格式

```
POST /_bulk
{"action": {"meta"}}\n
{"data"}\n
{"action": {"meta"}}\n
{"data"}\n

[
    {
        "action":{
            "method":"create"
        },
        "data":{
            "id":1,
            "field1":"java",
            "field1":"spring",
        }
    },
      {
        "action":{
            "method":"create"
        },
        "data":{
            "id":2,
            "field1":"java",
            "field1":"spring",
        }
    }       
]
```

- bulk中的每个操作都可能要转发到不同的node的shard去执行

- 如果采用比较良好的json数组格式

  允许任意的换行, 整个可读性非常棒, 读起来很爽, es拿到那种标准的json串后, 要按照下述流程去进行处理

  - 将json数组解析为jsonarray对象, 这个时候, 整个数据, 就会在内存中出现一份一模一样的拷贝, 一份数据是json文本, 一份数据是jsonarray对象
  - 解析json数组里的每个json, 对每个请求的document进行路由
  - 为路由到同一个shard上的多个请求, 创建一个请求数组, 100请求中有10个是到P1.
  - 将这个请求数组序列化
  - 将序列化后的请求数组发送到对应的节点上去.

- 耗费更多内存, 更多jvm gc开销

  我们之前提到过的bulk size最佳大小的那个问题, 一般建议说在几千条那样, 然后大小在10MB左右, 所以说, 可怕的事情来了. 假设说现在100个bulk请求发送到了一个节点上去, 然后每个请求时10M, 100个请求, 就是1G, 然后每个请求的json都拷贝一份为jsonarray对象, 此时内存中的占用就会翻倍, 就会占用2GB的内存, 甚至还不止. 因为弄成jsonarray之后, 还可能会多高一些其他的数据结构, 2GB+的内存占用. 

  占用更多的内存可能就会挤压其他请求的内存使用量, 比如说最重要的搜索技术, 分析请求, 等等. 此时就可能会导致其他请求的性能急速下降. 

  另外的话, 占用内存更多, 就会导致java虚拟机的垃圾回收次数更多, 跟频繁, 每次要回收的垃圾对象更多, 耗费的时间更多, 导致es的java虚拟机停止工作线程的时间更多. 

- 现在的奇特格式

  ```
  POST /_bulk
  { "delete": { "_index": "test_index",  "_id": "5" }} \n
  { "create": { "_index": "test_index",  "_id": "14" }}\n
  { "test_field": "test14" }\n
  { "update": { "_index": "test_index",  "_id": "2"} }\n
  { "doc" : {"test_field" : "bulk test"} }\n
  ```

  - 不用将其转换为json对象, 不会出现内存中的相同数据的拷贝, 直接按照换行符切割json
  - 对每两个一组的json, 读取meta, 进行document路由
  - 直接将对应的json发送到node上去

- 最大的优势在于, 不需要将json数组解析为一个jsonarray对象, 形成一份大数据的拷贝, 浪费内存空间, 尽可能地保证性能. 

