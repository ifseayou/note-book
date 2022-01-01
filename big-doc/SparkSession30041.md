# Yarn 资源调度器

## 报错如下 

有一个任务在执行的时候报如下异常，重试可以成功

```SQL
ERROR : FAILED: Execution Error, return code 30041 from org.apache.hadoop.hive.ql.exec.spark.SparkTask. Failed to create Spark client for Spark session a22c4b3c-c90a-4608-b017-e671e858c098_0: java.util.concurrent.TimeoutException: Client 'a22c4b3c-c90a-4608-b017-e671e858c098_0' timed out waiting for connection from the Remote Spark Driver
```

## 原因分析

网上列举的原因如下

* spark没有正常启动，排除改原因
* spark 和 hive 版本不匹配，排除该原因

* Hive 连接 spark client 超过设定时长

如果排除`hive`、`spark`配置，以及版本冲突等原因。您可以查看队列资源。如果队列资源达到100％，并且没有释放的空闲任务资源可在短时间内创建Spark会话，则任务将失败并且将引发此异常。

## 解决方法

increase the connection time interval of hive client to 5 minutes or more;

```sql 
set hive.spark.client.server.connect.timeout=300000 ;

或者修改 `hive/conf/hive-site.xml`

​```xml
<property>
    <name>hive.spark.client.connect.timeout</name>
    <value>300000ms</value> -- 默认是 90000ms
</property>
```

* 当集群资源使用率过高时可能会导致Hive On Spark查询失败，因为Yarn无法启动Spark Client。

* Hive在将Spark作业提交到集群是，默认会记录提交作业的等待时间，如果超过设置的`hive.spark.client.server.connect.timeout`的等待时间则会认为Spark作业启动失败，从而终止该查询

![image-20211012195754233](C:\Users\benchen\AppData\Roaming\Typora\typora-user-images\image-20211012195754233.png)

![image-20211012200540197](C:\Users\benchen\AppData\Roaming\Typora\typora-user-images\image-20211012200540197.png)



**如图所示：**

* Hive Client Session 创建 sparkClient 之前 会验证(Yarn ResourceManager)是否有足够的资源（计算资源，节点、参数限制）、调度器（资源调度队列）是有足够，如果足够创建SparkClient 
* 如果资源不满足，就等，默认等待时间   hive.spark.client.server.connect.timeout 为90S ，超过了该时间还没有相应，就是30041
* 解决方案：就是 `set hive.spark.client.server.connect.timeout=300000 ;`







公司的集群基于CDH，而CDH默认使用的调度器是公平调度器，Apache默认使用的容量调度器，还有一个种FIFO在生产环境中不会使用，

* FIFO，单队列，first in  first out
* Capacity 多队列，先进入的任务优先执行
* Fair 多队列，每个任务公平享有队列资源

这三种调度器详细区别可以参考[[hadoop的yarn资源队列](https://www.cnblogs.com/libin2015/p/12748357.html)]

调度器默认就一个队列，不能满足生产需求，一般会创建多个队列，多队列能够实现任务的降级使用，特殊时间的重要任务队列有充足的资源，也可以有效的避免由于代码bug(无限递归)导致的资源枯竭，创建多队列的可以按照如下分类：

* 按照框架，也就是按照每个框架的任务放入指定的队列（hive/spark/flink)  ,
* 按照业务模块：登录注册，下单，购物车，退款

这个问题还是需要具体在探究一下。

## hive on spark

把Spark作为Hive的一个计算引擎，将Hive的 查询作为Spark的任务提交到Spark集群上进行计算。通过该项目，可以提高Hive查询的性能，同时为已经部署了Hive或者Spark的用户提供 了更加灵活的选择，从而进一步提高Hive和Spark的普及率。







