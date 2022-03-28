# hash-join[^1]

> The **hash join** is an example of a [join algorithm](https://en.wikipedia.org/wiki/Join_(SQL)) and is used in the implementation of a [relational](https://en.wikipedia.org/wiki/Relational_database) [database management system](https://en.wikipedia.org/wiki/Database_management_system). All variants of hash join algorithms involve building [hash tables](https://en.wikipedia.org/wiki/Hash_table) from the tuples of one or both of the joined relations, and subsequently[^2] probing those tables so that only tuples with the same hash code need to be compared for equality in equijoins.
>
> Hash joins are typically more efficient than nested loops joins, except when the probe side of the join is very small. They require an equijoin predicate (a predicate comparing records from one table with those from the other table using a conjunction of equality operators '=' on one or more columns).

哈希关联

哈希关联是关联算法的一个例子，被应用在关系型数据库关联系统的实现中。各种各样的哈希关联算法都涉及从一张表或参与关联的所有表的元组中构建哈希表。 并随后探测这些表，以便于在等值关联中，只有具有相同哈希值的元组去比较是否相等。

哈希关联比嵌套关联更高效，但是在关联的探测端很小的时候除外。哈希关联要求一个等值关联谓词(一个谓词从一个表到另外的其他（在一列或者多列上使用了等值操作符“=”连接词）表比较记录)，

> ## Classic hash join
>
> The classic hash join algorithm for an [inner join](https://en.wikipedia.org/wiki/Join_(SQL)#Inner_join) of two relations proceeds[^3] as follows:
>
> - First, prepare a [hash table](https://en.wikipedia.org/wiki/Hash_table) using the contents of one relation, ideally whichever one is smaller after applying local predicates. This relation is called the build side of the join. The [hash table](https://en.wikipedia.org/wiki/Hash_table) entries[^4] are mappings from the value of the (composite[^5]) join attribute to the remaining attributes of that row (whichever ones are needed).
> - Once the [hash table](https://en.wikipedia.org/wiki/Hash_table) is built, scan the other relation (the probe side). For each row of the probe relation, find the relevant rows from the build relation by looking in the [hash table](https://en.wikipedia.org/wiki/Hash_table).
>
> The first phase is usually called the **"build" phase**, while the second is called the **"probe" phase**. Similarly, the join relation on which the hash table is built is called the "build" input, whereas the other input is called the "probe" input.
>
> This algorithm is simple, but it requires that the smaller join relation fits into memory, which is sometimes not the case. A simple approach to handling this situation proceeds as follows:
>
> :one:For each tuple $r$ in the build input $R$
>
> 1. Add $r$ to the in-memory hash table
>
> 2. If the size of the hash table equals the maximum in-memory size:
>
>    1. Scan the probe input $S$, and add matching join tuples to the output relation
>    2. Reset the hash table, and continue scanning the build input $R$
>
>    :two:Do a final scan of the probe input$R$ and add the resulting join tuples to the output relation
>
>    This is essentially the same as the [block nested loop](https://en.wikipedia.org/wiki/Block_nested_loop) join algorithm. This algorithm scans $R$ eventually more times than necessary.

经典哈希关联

对2个关系收益的inner join 的经典的哈希关联算法如下：

:a: 第一，使用其中（理想情况在应用本地谓词后更小的）一个关系准备哈希表。这个关系叫做关联的构建侧。**哈希表条目是从(合成的)关联属性值到该行的剩余属性的映射（无论需要哪个）**。

:b: 一旦哈希表构建完，扫描探测端的其他关系，对于探测端的每一行，通过查找哈希表的方式从构建关系中找到相关的行

第一阶段通常叫做构建阶段，然后这第二阶段叫做探测阶段。相似地，构建哈希表的那个关系叫做构建输入，然而其他的输入叫做探测输入。

这个算法很简单，但是要求较小的那个关联关系能放进内存中，然后有时并不符合这种情形。处理这种情况的一种简单的方式如下；

:a: 对于构建输入R中的每个 元组r

* 将 r 添加到内存哈希表中
* 如果哈希表的大小等于内存值的最大值
  * 扫描探测输入S，并且添加符合关联的元组到输出关系中
  * 重置哈希表，继续扫描构建输入R

:b: 对探测输入R的进行最后扫描，并且将关联元组的结果放入到输出关系中

这本质上和BNLJ 算法是相似的，这个算法最终，扫描R的次数多余必要的次数。

> ## Grace hash join
>
> A better approach is known as the "grace hash join", after the GRACE database machine for which it was first implemented.
>
> This algorithm avoids rescanning the entire {\displaystyle S}![S](https://wikimedia.org/api/rest_v1/media/math/render/svg/4611d85173cd3b508e67077d4a1252c9c05abca2) relation by first partitioning both {\displaystyle R}![R](https://wikimedia.org/api/rest_v1/media/math/render/svg/4b0bfb3769bf24d80e15374dc37b0441e2616e33) and {\displaystyle S}![S](https://wikimedia.org/api/rest_v1/media/math/render/svg/4611d85173cd3b508e67077d4a1252c9c05abca2) via a hash function, and writing these partitions out to disk. The algorithm then loads pairs of partitions into memory, builds a hash table for the smaller partitioned relation, and probes the other relation for matches with the current hash table. Because the partitions were formed by hashing on the join key, it must be the case that any join output tuples must belong to the same partition.
>
> It is possible that one or more of the partitions still does not fit into the available memory, in which case the algorithm is recursively applied: an additional orthogonal hash function is chosen to hash the large partition into sub-partitions, which are then processed as before. Since this is expensive, the algorithm tries to reduce the chance that it will occur by forming the smallest partitions possible during the initial partitioning phase.





> ## Hybrid hash join
>
> The hybrid hash join algorithm[[1\]](https://en.wikipedia.org/wiki/Hash_join#cite_note-1) is a refinement of the grace hash join which takes advantage of more available memory. During the partitioning phase, the hybrid hash join uses the available memory for two purposes:
>
> 1. To hold the current output buffer page for each of the {\displaystyle k}![k](https://wikimedia.org/api/rest_v1/media/math/render/svg/c3c9a2c7b599b37105512c5d570edc034056dd40) partitions
> 2. To hold an entire partition in-memory, known as "partition 0"
>
> Because partition 0 is never written to or read from disk, the hybrid hash join typically performs fewer I/O operations than the grace hash join. Note that this algorithm is memory-sensitive, because there are two competing demands for memory (the hash table for partition 0, and the output buffers for the remaining partitions). Choosing too large a hash table might cause the algorithm to recurse because one of the non-zero partitions is too large to fit into memory.































[^1]:wiki address: https://en.wikipedia.org/wiki/Hash_join
[^2]: subsequently 随后
[^3]: proceeds 收益	
[^4]: entries  条目; entry  条目 ，入口
[^5]:composite 合成的
[^6]:essentially 本质上	

