### 					 大数据工程师											

#### 个人信息

**姓	    名**：谢海龙								**性        别**：男<br>**年        龄**：24								**籍        贯**：河南信阳<br>**工作经验**：3年								**教育背景**：辽宁工程技术大学(本一)，网络工程<br>**电        话**：13126505729						**邮        箱**：ifseayou@gmail.com<br>

#### 求职方向

**从事岗位：**大数据研发工程师					**期望薪资**：面议

#### 工作经验

2017年9月至今 **  `大数据研发`                                                                                                                                                                                               

#### 技能栈

:one: 熟练使用Java、Scala ，掌握Python，Shell，熟悉JVM，了解JVM调优；

:two: 掌握关系型数据MySQL 、内存数据库`Redis`，文档数据库MongoDB，ES，时序库`InfluxDB`；

:three: 掌握常见大数据框架：

* 理解**Spark**架构、原理、Spark 作业提交流程（翻过源码）；熟练掌握常见RDD算子；<br>熟练使用Spark-SQL完成数据统计，查询，清洗；熟练掌握Spark Streaming，Spark Core；<br>掌握常见Spark的调优策略，了解`Spark-MLlib`；
* 掌握**HDFS**读写流程，MR Shuffle机制，**YARN **Job提交流程；熟悉**Hadoop**常见调优策略；<br>熟练掌握Hadoop分布式集群的安装，部署，配置和测试，熟悉Hadoop生态圈的应用开发；
* 掌握**Hive**架构，熟练掌握窗口函数、UDF、UDTF、UDAF，熟练运用HQL语句完成数据的统计、查询、清洗等，掌握常见Hive调优手段；掌握**Impala**和**`Kylin` **；
* 熟悉**Flume**的Source，Channel，Sink组件，及组件的常用类型，及其类型适配 ；
* 理解消息订阅模型，掌握**Kafka**架构、工作流、高吞吐原理、存储机制；<br>熟练使用Kafka Producer、Consumer、Stream 实现数据高吞吐的处理；了解Kafka Connect数据管道；
* 熟练使用**`Sqoop`**完成数据的导入导出；熟悉**Azkaban**完成作业调度；
* 理解**Zookeeper**运行原理及分布式锁概念 ，掌握Zookeeper集群部署实现高可用；
* 熟悉**`HBase`**设计理念和架构，掌握`HBase`读写过程，掌握`Rowkey`的设计原则，掌握常见`HBase`优化方式；

:four: 掌握**`SpringMVC`、Spring、`Mybatis`**等Web框架；熟悉**Spring Boot、Spring Cloud**微服务框架；

:five: 掌握基本的算法和数据结构；熟悉常见机器学习算法

:six: 了解推荐系统，如User-CF，Item-CF，TF-IDF，CB，LFM等；

:seven: 掌握Windows和Linux环境，掌握 `Git`，Maven，IDEA等常用开发工具完成个人或协作开发；

#### 语言&社区

**英语**：CET-6，经常浏览国外技术站点，技术官网。

**口语** ：能满足基本英语口语交流与沟通。

**社区经历**：GitHub: https://github.com/ifseayou Blog：

#### 项目

**项目1：基于用户行为的个性化电影推荐系统**

**技术**：Scala + Java + Flume + Kafka + Spark + Redis +MongoDB + Elasticsearch

**具体实现：**

1、使用Flume收集用户日志，例如评分行为、点击行为、浏览行为。 

2、Kafka实时读取Flume收集的日志信息。 

3、Spark Streaming消费Kafka队列的数据。 

4、使用ALS算法对评分矩阵做矩阵分解，根据电影的隐语义特征计算电影之间的相似度，并将相似度做倒排索引，例如{'movieid1':[('movieid2','0.99'), ...]},并将倒排数据持久化到MongoDB。 

5、利用电影的标签数据，使用TF/IDF来计算电影之间的相似度，同样使用倒排的思路持久化道MongoDB。 

6、实时推荐：利用电影的相似度倒排，根据用户最新的电影评分或者点击行为来做推荐，使用Spark Streaming来实时计算推荐优先级，然后存储到Redis中，提高用户的访问体验。 

7、使用Spark计算每个门类的平均评分最高的电影来解决冷启动问题。 

8、使用Spark将日志数据做分析和处理，然后持久化道MongoDB、ES等数据库中，实现data loader功能。 

9、使用了业界广泛使用的MovieLens数据集，并了解过Lastfm、Netflix等著名的数据集。 10、将推荐系统引擎模块化：ASL矩阵分解的相似度计算、基于TF-IDF的相似度计算、实时推荐模块，每一个引擎都会产生一个推荐列表，对不同的引擎赋予不同的权重，然后合并列表，产生推荐数据。 

11、优化Spark的计算效率，比如将一些数据进行.cache()操作缓存，对某些数据做broadcast广播到其他节点，加快运算。

 

**项目2：超级淘数据仓库离线/实时分析项目**

**技术**：Hadoop +Flume +Kafka +Sqoop +Redis +Canal +Spark +ES +Kibana +MySQL；

**数仓+离线分析**：

1） Nginx实现日志服务器的负载均衡，Flume采集Tomcat的日志信息，自定义过滤器和选择器提取Body的分类字段置于Header中，实现日志数据的分类。

2） Flume的Source组件使用TailDir类型，实现数据的断点续传，避免数据重复问题

3） 数据建模：将数据仓库划分为四层，ODS层存储日志和数据库原始信息；DWD层，完成对数据的轻度过滤，将雪花模型转化为星型模型，将事实表和维度表进行关联，把多维降为单维，并改存储方式为列式存储，压缩方式为Snappy；DWS对数据进行聚合，即多表Join，形成面向多业务指标的宽表；ADS层形成最终的结果表。

4） 为应对数据量巨大，但是会发生变化的表，制作拉链表，如用户表和订单表

5） 编写Shell脚本，实现每日新增的数据，逐层进入数据仓库，并使用Azkaban实现任务调度。

6） 分析诸如UV，GMV，留存，转化，复购率等离线指标

**实时分析：**

1)       使用Flume进行日志数据的采集并传输到Kafka，Canal检测结构化数据的变化量，传输到Kafka，Kafka作为暂存数据缓存，进一步做实时分析 ；

2)       Spark Streaming消费Kafka中的数据，并进行实时计算，并将计算结果存储到Elasticsearch中；

3)       Spark Streaming使用广播变量将Redis中存储的数据，广播到Executor优化计算；

4)       设计Elasticsearch的索引结构和分词类型，便于实时查询Elasticsearch中的数据；

5)       Redis做为实时数据的缓存服务器，用于过滤重复的实时数据；

6)       Elasticsearch使用IK分词器，用来支撑用户的中文查询和检索功能；

7)       分析实时交易额，转化率，日活，复购率等指标；

#### 个人兴趣&评价

游泳，写作，冲浪；

坚韧，乐观，自我驱动，喜欢合作，较强的学习和抗压能力；热爱技术。

低调的trouble shooting 能力。

有些完美主义，目标驱动。