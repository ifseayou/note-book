# Execution plan

执行/查询计划老外有三种叫法：Execution plan/query explanation paln/query plan[^1]。这个概念起源于关系型数据库，后来随着开源OLAP引擎同样follow了RDB的传统，实现了查询计划。关于执行计划，要注意：执行计划是*优化器/执行器*打算访问数据的步骤，所以实际并没有真的执行

## 1-MySQL 执行计划

下图是MySQL的执行计划[^2]的例子

<img src="./img/qp/01.jpg" width = 100% height = 70% alt="图片名称" align=center />

> 实践中，`ref_or_null`类型看到的还是比较少的；为什么无须回表的`index`的要劣于`range`？举个:chestnut:` goods_name`字段上有索引，对于 `select goods_name from goods where goods_name like '%果%'`，即便有索引，且无须回表，但是还需要全索引扫描的

MySQL的执行计划输出[^3]，Oracle官网有详细的描述，可以前往查看

## 2-PostgreSQL执行计划

<img src="./img/qp/02.jpg" width = 100% height = 70% alt="图片名称" align=center />

上图是PostgreSQL的执行计划[^4]的例子，①图和②图的区别是②中增加了`analyze`参数，该参数会触发当前查询实际执行；③号图中的执行计划可以缩略为④号图，类似编程语言中的函数调用

<img src="./img/qp/03.jpg" width = 100% height = 70% alt="图片名称" align=center />

> 关于PostgreSQL中的 seq_scan,index_scan,bitmap scan[^5]：
>
> :one:seq_scan：全表扫描 when select a LOT of data from a table
>
> :two:index_scan:  Index Only Scan  when select a handful of rows
>
> :three:bitmap scan :  too much row for an index scan to be efficient but too little for a sequential scan，如下图：
>
> <img src="./img/qp/04.jpg" width = 100% height = 70% alt="图片名称" align=center />

PostgreSQL的执行计划输出，PostgreSQL官网[^6][^7]有详细的描述，可以前往查看

## 3-impala执行计划

关于impala本身，你必须知道它是一个MPP SQL引擎。impala的执行计划官网上描述的很少，全是俗媚的描述性内容，参考意义不大，阅读impala paper[^8]，观看cloudera出品的教学视频[^9] 是一个更上头的操作，要区分impala的 execution plan 和 profile，前者是未执行就可获知，后者需要执行后才能获取。下图中上半部分来于视频，下半部分来自于论文

<img src="./img/qp/05.jpg" width = 100% height = 70% alt="图片名称" align=center />

就上图做以下几点说明：

:one: 从SQL查询流中可看出，客户端提交的SQL会被Impala前台编译为执行计划，再交给impala后台执行，最后将查询结果/查询简介返回给客户端

:two: 从Impala逻辑视图中看出，impala逻辑上分为3个模块(虚线圈出)：元数模块+执行模块+存储模块

:three: 上图的下半部分暂时了一个SQL在impala的整个生命周期(需要标记出)

<img src="./img/qp/06.jpg" width = 100% height = 70% alt="图片名称" align=center />

上图(上半部分来源于视频，下半部分来源于论文)展示了Impala的执行计划分为2个阶段，

:a:single node plan : 单机上的执行计划

:b:distributed node plan : 分布式执行计划，全局内 哪些查询是并行的，哪些是需要数据交换(exchance)的

可以看到distributed plan相对于single plan增加了Exchange，这是因为如果数据分布在不同的节点上，我们需要使用使用类似Grace Hash Join的方式使得相同的key去往同一个节点，这个类似Hive/Spark中shuffle的行为，在impala中称之为exchange







🔞Reference

[^1]:query plan wiki : https://en.wikipedia.org/wiki/Query_plan
[^2]: mysql query plan : https://www.youtube.com/watch?v=9K26Wb84f50
[^3]: mysql explain output format: https://dev.mysql.com/doc/refman/8.0/en/explain-output.html

[^4]:postgresql query plan : https://www.youtube.com/watch?v=Mll5SqR4RYk&t=632s

[^5]: seq_scan,inde_scan,bitmap_scan: https://www.cybertec-postgresql.com/en/postgresql-indexing-index-scan-vs-bitmap-scan-vs-sequential-scan-basics/
[^6]:  postgresql explain output format : https://www.postgresql.org/docs/10/using-explain.html | ↩
[^7]:  postgresql performance tuning:  https://www.postgresql.org/docs/8.1/performance-tips.html

[^8]: impala parper : http://www.cidrdb.org/cidr2015/Papers/CIDR15_Paper28.pdf
[^9]: impala tutorial of cloudera : https://www.youtube.com/watch?v=J0n-yORrmcU

