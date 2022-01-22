### 02-关于谓词下推

#### 大规模数据：学习谓词下推如何省钱[^1]

> Data at Scale[^2]: Learn How **Predicate Pushdown Will Save You Money**
>
> Even if you spend a lot of time working with data at scale, you might not be aware of Predicate Pushdown and its importance when building products. Wonder what is it good for and why? read this.

即便你从事大规模数据处理的工作已经很久了，在构建应用的时候，你可能也没有意识到谓词下推的重要性，想知道谓词下推的好处和其中的原理吗？读下面这篇文章：

> What is Predicate Pushdown?
>
> *Predicate Pushdown* gets its name from the fact that portions of SQL statements, ones that filter data, are referred to as predicates. They earn that name because predicates in mathematical logic and clauses in SQL are the same kind of thing — statements that, upon evaluation, can be TRUE or FALSE for different values of variables or data.
>
> It can improve **query performance** by **reducing** the amount of data read (I/O) from Storage files. The Database process evaluates filter predicates in the query against **metadata stored** in the Storage files.

##### 什么是谓词下推？

在一个SQL中，起到过滤数据作用的，就是谓词；因为谓词在数学中有过滤数据的作用，所以在SQL语句中，有过滤作用的也叫谓词，他们的本质都是，在评估时，对于不同的变量的值或者数据的值，可以表示未 FALSE 或者 TRUE。

谓词通过减少从存储文件读取数据量的方式，提升查询性能。在查询时，数据库的进程通过存储在存储文件中的元数据评估过滤谓词。

>How does Predicate Pushdown helps process Data at Scale?
>
>The metadata helps the storage process to decide which files are relevant for reading before that happens.
>
>- If you issue[^3] a query in one place to run against a lot of data that’s in another place, you could spawn[^4] a lot of network traffic, which could be slow and costly. But …
>- … if you can “push down” parts of the query to where the data is stored, and thus filter out most of the data, then you can greatly reduce network traffic[^5].
>- given the storage metadata, “push down” help us decided which files are relevant and which are not.

#####  谓词下推是怎么提升大规模数据处理的性能的？

在实际的查询发生之前，元数据会帮助存储进程判断哪些文件是查询相关的文件

:one: 如果你从一个位置的查询是从另外一个地方获取大量的数据，这回产生很大的网络流量，高延迟而且代价高。

:two: 如果你可以将一部分查询下推到数据存储的地方，由于可以过滤大部分数据，因此可以显著的减少网络流量

:three: 通过存储元数据，下推帮助存储进程判断哪些文件是目标查询相关的文件



> A few prerequisites for successful pushdown:
>
> - Storage is balanced :
>
> Means that the metadata files size is smaller than reading the actual data files.
>
> ```java
> metadataFiles.size() << actualFiles.size()
> ```
>
> - Storage accurate statistics, metadata and indices.

##### 成功的谓词下推的先决条件：

* 存储是平衡的：存储是平衡的意味着 元数据文件的大小小于读取的实际文件大小

* ==存储精确的统计信息，元数据和索引==



> Why should we care
>
> When processing data at scale we look at the next bullet points[^6] that might hurt our pocket:
>
> * **Network traffic** (sending data over the network)
>
> * **I/O** ( reading disk files are bounded to disk reading speed)
>
> * **Time** (product SLAz[^7] goals — processing time agreed upon)
>
>   > When we use the full power of push-down predicate, we save money by reading only the data we need . Which results in faster query processing. Hence, pay only for what is used.
>
> On top of that, we save in network traffic, I/O and precious[^8] time.

##### 为什么我们应该关心：

在处理大规模数据的时候，我们应该关注那些可能伤害我们口袋的要点：

:one: 网络流量（通过网络传输的数据）

:two: I/O (读取磁盘文件受磁盘读取速度的限制)

:three: 时间（产品SLA目标-被允许处理时间的上线）

充分使用谓词下推时，我们可以仅仅读取我们需要的数据，如此一来，就可以获得更快的处理时间，仅仅为我们使用的那部分付费，从而达到省钱的目的。

综上，我们节省了网络流量，IO和宝贵的时间。

[^1]:https://medium.com/microsoftazure/data-at-scale-learn-how-predicate-pushdown-will-save-you-money-7063b80878d7
[^2]: data at scale 大规模数据
[^3]: issue: 发起
[^4]: spawn : 产卵
[^5]: traffic : 交通，network traffic 网络流量
[^6]: bullet ：子弹，bullet point 要点
[^7]: service-level agreement , https://zh.wikipedia.org/wiki/%E6%9C%8D%E5%8A%A1%E7%BA%A7%E5%88%AB%E5%8D%8F%E8%AE%AE
[^8]: precious : 宝贵的；precise 精确 ； 注意区分两者







