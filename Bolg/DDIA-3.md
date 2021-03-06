# 设计数据密集型应用(三)，DDIA

### 五、第7章节-事务

#### 5.1-事务的起源

很早就接触事务这个概念，关于事务网上的文章动不动就把转账的的例子拿出来讲，坑的时候有的压根就没有讲明白，事务的概念**事务要不就执行成功，要不执行失败，只有这2种状态**也背的烂熟，也知道事务的4大特性ACID (原子性、一致性、隔离性、持久性)，但是这么些年从来没有思考过：为什么要有事务？他解决了什么样子的问题/痛点？那么我们带着这个问题来回顾一下事务起源：

<img src="D:/08-work/note-book/book-doc/img/ddia/36.jpg" width = 100% height = 70% alt="图片名称" align=center />

上图中有一个名为猪小明程序员抱着自己的电脑在疯狂的写代码(开发应用程序)，其中应用程序需要透过网络在数据库中存放数据/获取数据，数据库软件依托于是操作系统，操作系统的底层是一些计算机硬件(存储介质/磁盘/缓存/Cache/RAM /ROM/CPU/主板等)。整个过程中，各个环节都有可能出错，比如：

:one: 网络中断，客户端和应用程序服务之间，服务和数据库之间

:two: 数据库软件本身挂掉了，硬件发生故障[^9]

> [^9]: 关于硬件故障：硬件故障率是很高的，磁盘在进行大量的读写之后失效的概率是很高的，IDC数据中心对物理环境的要求是很苛刻的，为了降低温度空调不够用，甚至把数据中心搬到山洞里，比如阿里在贵州云南的IDC，地板使用静电地板，每个机房入口的挡鼠板比膝盖还高，供电都是双路供电等。

:three: 应用程序在进行写入的时候，写到一半，自己崩溃了

:four: 多个客户端同时操作数据库，覆盖彼此的更新

:five: 客户写到一半的数据，被另外一个客户读取到

:six: 客户之间的竞争导致的令人惊讶的错误

所有的这一些都需要应用程序的开发者猪小明去解决，但是这个工作量是巨大的，应用开发应该专注于业务，而不是通用问题的处理，这些问题应该留给下层的数据库去处理。所以为了**简化应用编程模型** ，事务诞生了。通过使用事务，应用程序可以自由地忽略某些潜在的错误情况和并发问题，因为数据库会替应用处理好这些问题

1974年的时候，IBM的圣荷西研究中心发布了第一款提供优秀的事务处理能力的关系型数据库R[seminal-project]。时至今日开发者已经习惯了事务，他们觉得事务是理所当然的，是天然就存在的，然而并不是，了解事务出现的历史，我们可以发现事务是我们的计算机先驱们为了解决一揽子问题，提供的一种解决方案

#### 5.2-ACID

谈及事务，必谈事务的4大特性ACID；那么ACID 分别指的是什么？

***A**tomicity* ： 原子性。**能够在错误时中止事务，丢弃该事务进行的所有写入变更的能力**，可以理解为**可终止性**。假设没有原子性，如果有多次更改，但是更改发生过程中发生了错误，应用程序很难判断哪些更改生效了，哪些没有生效。如果有了原子性，应用程序可以确定的知道在发生错误时，所有更改没有生效。**没有原子性，错误处理就会变的很复杂**

> 区别于线程的原子操作：多线程中的原子操作描述的是如果一个线程执行一个原子操作，意味着另外一个线程无法看到该原子操作的中间结果。而这个特性在ACID中是 *I(isolation)* 来描述的

***C**onsistency*：一致性。在事务中，一致性是指：**对数据的一组特定陈述必须始终成立**，即为*不变量*，如在会计系统中，所有账户整体上必须借贷相抵。原子性，隔离性和持久性是数据库的属性，而一致性(在ACID意义上)是应用程序的属性

> 这是一个一词多意的词，用行话来说，这个词被重载了
>
> * 有别于副本一致性，比如在单主复制的模式下，采用异步复制的方式，收敛最终一致
>
> * 还有大家有可能会听过一致性hash(一致性散列)那是一种为了避免重新分区带来的复杂度提高的一种解决方案
> * CAP定理中，C指的是线性一致性

***I**solation*：隔离性。**竞争条件下，同时执行的事务是相互隔离的**，下图是两个客户之间的竞争状态同时递增计数器的图述。**缺乏隔离性，就会导致并发问题**

<img src="D:/08-work/note-book/book-doc/img/ddia/02.jpg" width = 100% height = 70% alt="图片名称" />

***D**urability* ：持久性。持久性是事务的一个承诺，也即事务完成后，即便发生硬件故障或者数据库崩溃，写入的任务数据也不会丢失。持久性过去一般被认为写入了非易失性存储介质

####  5.3-单对象操作和多对象操作

**单一对象操作**，所谓单对象操作中的对象指的是数据库中被修改的对象，比如你正在向数据库写入一个20KB的Json文档，以下场景可能会发生：

:one: 在发送第一个10KB之后，网络连接中断，数据库是否存储了不可解析的10KBJSON片段？

:two: 在数据库正在覆盖的磁盘上前一个值的过程中电源发生故障，是否最终将新旧值拼接在一起？

:three: 如果另一个客户端在写入过程中读取该文档，是否会看到部分更新？

这里的 JSON对象，就是单一对象，单一对象是相对于多对象而言的，待会我们会谈及到多对象。为了针对以上的问题，存储引擎会在单个对象上提供原子性和隔离性，如此一来：

:a: 原子性通过WAL日志来实现崩溃恢复

:b: 使用每个对象的锁来实现隔离(每次仅仅允许一个线程访问对象)

**CAS**，为了防止多个客户端同时写入同一个对象时的更新丢失，除了单对象操作，还有就是CAS(Compare-and-set)，也就是当值没有并发被其他人修改的时候，才允许执行写入操作。CAS操作和单对象操作，被称作是轻量级事务。**事务通常更多的强调 ： 将多个对象的多个操作合并为一个执行的单元的机制**。

**何为多对象？** 在操作数据库时，需要协调写入几个不同的对象：

* 关系模型中，一个表中的行对另外一个表的外键引用。你得确保外键是最新的，可用的
* 在字段冗余的场景中，单个字段在多处被存储，你得保证这几处是同步的
* 二级索引的数据库中，数据更新的时候，二级索引也需要更新

在这种情形下，需要使用事务来进行处理。

接下来我们会讲述隔离级别，在讲述隔离级别之前，明确两点：

:a: 隔离级别是对事务的4大特性之一隔离性上进行了一个等级划分，数据库标准的事务隔离级别包括：

* 读未提交(read uncommitted)
* 读已提交(read committed)
* 可重复读/快照隔离(repeatable read)
* 串行化(serializable)

:b: 隔离级别最高是可序列化，表示同一时间只能有一个事务。隔离级别和性能之间是一个负相关的关系，也就是说隔离级别越高，数据一致性的保证越好，但是性能越差。隔离级别是数据一致性和服务性能的一场博弈。为了实现更优的性能，我们需要较弱的隔离级别

下面介绍这些弱隔离级别

#### 5.4-读已提交

最基本的弱隔离级别是，读已提交，它提供了两个保证：

* 没有**脏读(dirty reads)**，从数据库读时，只能看到已提交的数据
* 没有**脏写(dirty writes)**，写入数据库时，只会覆盖已经写入的数据

另外读未提交：

* 可以防止脏写
* 但是不能够防止脏读

下图是一个没有脏读的例子：可以看到*直到用户1提交了之后*，用户2才看到提交之后的值x=3,而在这之前用户3只能看到x=2

<img src="D:/08-work/note-book/book-doc/img/ddia/03.jpg" width = 100% height = 70% alt="图片名称" align=center /> 

那么为什么要防止脏读呢？主要是下面两个原因：

:one: 如果事务需要更新多个对象，脏读取意味着另一个事务可能会只看到一部分更新。比如说下面这个电子邮件的例子。事务还没有提交，但是用户2看到了未读邮件，可是未读邮件的数量却还是旧值

<img src="D:/08-work/note-book/book-doc/img/ddia/04.jpg" width = 100% height = 70% alt="图片名称" align=center /> 

:two: 若数据库允许脏读，意味着一个事务可能会看到稍后需要回滚的数据，即从未实际提交给数据库的数据。比如下面的例子中红色标记的位置读到了未提交的数据，但是后面事务回滚了

<img src="D:/08-work/note-book/book-doc/img/ddia/05.jpg" width = 100% height = 70% alt="图片名称" align=center /> 

没有脏写，在写入数据库时，只会覆盖已经写入的数据，也就是说两个事务同时更新数据库中的对象，先前的写入没有提交，后面的写入覆盖这个尚未提交的值，这就是脏写。在**读已提交**的隔离级别上运行的事务必须防止脏写，通常是延迟第二次写入，直到第一次写入事务提交或中止为止。下图是脏写发生的示例，发票属于Alice; 销售属于Bob

<img src="D:/08-work/note-book/book-doc/img/ddia/06.jpg" width = 100% height = 70% alt="图片名称" align=center /> 

#### 5.5-实现读已提交

读已提交是一种非常Fashion的一个隔离级别，有很多数据库软件将读已提交设置为默认的隔离级别，比如Oracle 11、PostgreSQL、SQLServer 2012 ，那么如何实现

:one: 无脏写保证：数据库通过使用**行锁(row-level lock)[^10]**来防止脏写；即当事务想要修改特定对象时，必须获取该对象的锁，然后必须持有该锁直到事务被提交或终止。这种锁定是读已提交模式(或更强的隔离级别)的数据库自动完成的

> [^10]: 行锁满足两阶段锁协议，两阶段锁协议是说：锁需要的时候才加上的，在事务结束的时候才释放；同时行锁也是2PL，在当前事务写入时必须持有排它锁，直到事务提交才释放排它锁

:two: 无脏读保证：MVCC[^11] ，数据库都会记住旧的已提交值，和当前持有写入锁的事务设置的新值。 当事务正在进行时，任何其他读取对象的事务都会拿到旧值。 只有当新值提交后，事务才会切换到读取新值

> [^11]: 同一条记录在系统中可以存在多个版本，这就是数据库的多版本并发控制；如下图中一个值从1按顺序修改为4的过程。在读已提交的隔离级别下，仅仅保留为提交版本和提交前版本2个版本
>
> <img src="D:/08-work/note-book/book-doc/img/myl/07.jpg" width = 100% height = 70% alt="图片名称" align=center /> 

读已提交的隔离级别无法避免*不可重复读*的情况，下面的例子：爱丽丝在银行有1000美元的储蓄，两个账户，每个500美元；现在一个事务从她的一个账户中转移了100美元到另一个账户

* Alice在转账事务之前查询了账户1的金额为500元
* Alice在转账之后完成之后，查询了账户2的金额为400元
* 此时账户的总额为900元，Alice就很疑惑为什么自己的钱少了？

<img src="D:/08-work/note-book/book-doc/img/ddia/07.jpg" width = 100% height = 70% alt="图片名称" align=center /> 

这种，这种异常被称为**不可重复读(nonrepeatable read)**，如果Alice在事务结束时再次读取账户1的余额，她将看到与她之前的查询中看到的不同的值(600美元)。在读已提交的隔离条件下，**不可重复读**可能会发生

#### 5.6-实现快照隔离

快照隔离的另外一个叫法是可重复读，快照隔离和读已提交一致，使用写锁来防止脏读，也就是正在进行写入的事务会阻止另外一个事务修改同一个对象。读取没有任何的锁定，**写不阻塞读，读不阻塞写**，RC下也是，数据库使用**多版本并发控制(MVCC, multi-version concurrentcy control)** 数据库保留一个对象的几个不同的提交版本。另外使用MVCC实现快照隔离的存储引擎通常也会使用MVCC来实现读已提交(一个对象的两个版本：提交的版本和被覆盖但尚未提交的版本)，如下面PostgreSQL的例子：

<img src="D:/08-work/note-book/book-doc/img/ddia/08.jpg" width = 100% height = 70% alt="图片名称" align=center /> 

那么我们再来看一下*一致性快照的可见性规则*：也就是说当一个事务从数据库中读取时，事务ID用于决定它可以看见哪些对象，看不见哪些对象。规则如下：

:one: 在每次事务开始时，数据库列出当时所有其他(尚未提交或中止)的事务清单，即使之后提交了，这些事务的写入也都会被忽略

:two: 被中止事务所执行的任何写入都将被忽略

:three: 由具有较晚事务ID(即，在当前事务开始之后开始的)的事务所做的任何写入都被忽略，而不管这些事务是否已经提交

:four: 所有其他写入，对应用都是可见的

更简单的讲，对于隔离级别的实现数据库里面会创建一个视图，访问的时候以视图的逻辑结果为准

:one: “读未提交”隔离级别下直接返回记录上的最新值，没有视图概念

:two: 在“读提交”隔离级别下，这个视图是在每个 SQL 语句开始执行的时候创建的

:three: 在“可重复读”隔离级别下，这个视图是在事务启动时创建的，整个事务存在期间都用这个视图

:four: “串行化”隔离级别下直接用加锁的方式来避免并行访问

#### 5.7-丢失更新 & 写偏差 

前面描述的是读-写并发场景下只读事务在并发写入时候能看到什么，另外一个问题是两个事务并发写入的问题，即写-写冲突。如下图就是**丢失更新**的例子

<img src="D:/08-work/note-book/book-doc/img/ddia/02.jpg" width = 100% height = 70% alt="图片名称" /> 

解决写-写冲突有很多方式，比如比较并设置(CAS)

```sql
update wiki_pages set content = '新内容'
  where id = 1234 and content = '旧内容';
```

在MySQL中，使用当前读[^12]的方式来处理写-写冲突，下图为RR隔离级别下，写-写冲突的例子

> [^12]: 更新数据是先读后写的，读只能读当前**已提交**的最新值，这就是当前读。

<img src="D:/08-work/note-book/book-doc/img/ddia/10.jpg" width = 100% height = 70% alt="图片名称" />

下面是写偏差的例子，描述的是一个医生轮班管理程序，医院有以下的要求：至少有一位医生在待命，现在Alice 和 Bob 两位值班医生都感觉到不适，决定请假：

<img src="D:/08-work/note-book/book-doc/img/ddia/09.jpg" width = 100% height = 70% alt="图片名称" align=center /> 

如果两个事务读取相同的对象，然后不同的事务可能更新不同的对象，则可能发生写偏差(写偏差包含丢失更新)。在多个事务更新同一个对象的特殊情况下，就会发生脏写或丢失更新(取决于时机) **防止写偏差需要使用序列化隔离级别**。下图是一个幻读的例子：

> 幻读会导致写偏差。快照隔离避免了只读事务中的幻读，但是无法避免读写事务中的幻读。从上面的例子来看，幻读的问题貌似是没有对象可以加锁。人为的引入锁对象的方式被称之为*物化冲突*

<img src="D:/08-work/note-book/book-doc/img/ddia/11.jpg" width = 100% height = 70% alt="图片名称" align=center /> 

:a: 主事务，检测表中是否有id为1的记录，没有则插入，这是我们期望的正常业务逻辑

:b: 干扰事务，目的在于扰乱主事务 的正常的事务执行

我们看到，干扰事务率先执行了，主事务发生了幻读，因为主事务读取的状态并不能支持它的下一步逻辑，感觉看到了幻影。**一个事务中的写入改变另一个事务的搜索查询的结果，称为幻读** ，上例中是干扰事务妨碍了主事务搜索结果。在MySQL中，使用间隙锁(下文会涉及到)来处理幻读问题。*不可重复读侧重表达读-读，幻读则是说读-写，用写来证实读的是鬼影*

#### 5.8-序列化/串行化

对串行化的理解应当是这样的：一次只执行一个事务。设计单线程的系统有时候比支持并发的系统更好，因为它可以避免协调锁的开销。数据库的早期，数据库意图包含整个用户的活动流程，但是如今的web应用，一个事务不会跨越多个请求，事务会在同一个Http请求被提交

##### 5.8.1- 两阶段锁定-2PL

30年以来，数据库中只有一种广泛使用的序列化算法：**两阶段锁定(2PL，two-phase locking)**。2PL要求只要没有写入，就允许多个事务同时读取同一个对象。但对象只要有写入(修改或删除)，就需要**独占访问(exclusive access)** 权限。锁可以处于*共享模式*，可以处于*独占模式*：

:one: 若事务要读取对象，则须先以共享模式获取锁。允许多个事务同时持有共享锁。但如果另一个事务已经在对象上持有排它锁，则这些事务必须等待

:two: 若事务要写入一个对象，它必须首先以独占模式获取该锁。没有其他事务可以同时持有锁(无论是共享模式还是独占模式)，所以如果对象上存在任何锁，该事务必须等待

:three: 如果事务先读取再写入对象，则它可能会将其共享锁升级为独占锁。升级锁的工作与直接获得排他锁相同

:four: 事务获得锁之后，必须继续持有锁直到事务结束(提交或中止)，这就是“两阶段”这个名字的来源：第:one:阶段(当事务正在执行时)获取锁，第:two:阶段(在事务结束时)释放所有的锁

在Java中有这么一个定律：对象(Object)就是锁，前面内容涉及到的锁都是针对特定对象的(如表中的一行)，对于某些更改没有特定对象，有没有一种锁针对这种场景呢？

##### 5.8.2-谓词锁

谓词锁类似于共享/排它锁，但不属于特定的对象（例如，表中的一行），它属于所有符合某些搜索条件的对象，如：

```sql
select * from bookings
where room_id = 123 and
      end_time > '2018-01-01 12:00' and 
      start_time < '2018-01-01 13:00';
```

谓词锁限制访问：

:a: 如果事务A想要读取匹配某些条件的对象，就像在这个 `select` 查询中那样，它必须获取查询条件上的**共享谓词锁(shared-mode predicate lock)**。如果另一个事务B持有任何满足这一查询条件对象的排它锁，事务A必须等到B释放它的锁之后才允许进行查询

:b: 如果事务A想要插入，更新或删除任何对象，则必须首先检查旧值或新值是否与任何现有的谓词锁匹配。如果事务B持有匹配的谓词锁，那么A必须等到B已经提交或中止后才能继续

谓词锁的关键思想是，**谓词锁甚至适用于数据库中尚不存在，但将来可能会添加的对象(幻象)**。和快照隔离的区别在是：快照隔离中读不阻塞写，写不阻塞读；2PL中，写阻塞读，读阻塞写

##### 5.8.3-索引范围锁

谓词锁的弊端是性能不佳：**如果活跃事务持有很多锁，检查匹配的锁会非常耗时**。因此，大多数使用2PL的数据库实际上实现了索引范围锁(也称为**间隙锁(next-key locking)**)，间隙锁是一种简化的近似版谓词锁

比如在房间预订数据库中，您可能会在`room_id`列上有一个索引，并且/或者在`start_time` 和 `end_time`上有索引(否则前面的查询在大型数据库上的速度会非常慢)

:a: 假设您的索引位于`room_id`上，并且数据库使用此索引查找123号房间的现有预订。现在数据库可以简单地将共享锁附加到这个索引项上，指示事务已搜索123号房间用于预订

:b: 或者，如果数据库使用基于时间的索引来查找现有预订，那么它可以将共享锁附加到该索引中的一系列值，指示事务已经将12:00~13:00时间段标记为用于预定

无论哪种方式，搜索条件的近似值都附加到其中一个索引上。现在，如果另一个事务想要插入，更新或删除同一个房间和/或重叠时间段的预订，则它将不得不更新索引的相同部分。在这样做的过程中，它会遇到共享锁，它将被迫等到锁被释放。这种方法能够有效防止幻读和写入偏差

####  5.9-序列化快照隔离（SSI）

**可序列化快照隔离(SSI, serializable snapshot isolation)** 它提供了完整的可序列化隔离级别，但与快照隔离相比只有只有很小的性能损失，是一种新的隔离技术

#### 5.10-总结

:one: 脏读: 一个客户端读取到另一个客户端尚未提交的写入。**读已提交**或更强的隔离级别可以防止脏读

:two: 脏写: 一个客户端覆盖写入了另一个客户端尚未提交的写入。几乎所有的事务实现都可以防止脏写

:three: 读取偏差(不可重复读): 在同一个事务中，客户端在不同的时间点会看见数据库的不同状态。**快照隔离**经常用于解决这个问题，它允许事务从一个特定时间点的一致性快照中读取数据。快照隔离通常使用**多版本并发控制(MVCC)** 来实现

:four: 更新丢失: 两个客户端同时执行**读取-修改-写入序列**。其中一个写操作，在没有合并另一个写入变更情况下，直接覆盖了另一个写操作的结果。所以导致数据丢失。快照隔离的一些实现可以自动防止这种异常，而另一些实现则需要手动锁定(`select for update`)

:five: 写偏差: 一个事务读取一些东西，根据它所看到的值作出决定，并将决定写入数据库。但是，写的时候，决定的前提不再是真实的。只有可序列化的隔离才能防止这种异常

:six: 幻读 : 事务读取符合某些搜索条件的对象，另一个客户端进行写入，影响搜索结果。快照隔离可以防止直接的幻像读取，但是写入歪斜环境中的幻读需要特殊处理，例如索引范围锁定。只有可序列化的隔离才能防范所有这些问题。我们讨论了实现可序列化事务的三种不同方法：

* 字面意义上的串行执行: 如果每个事务的执行速度非常快，并且事务吞吐量足够低，足以在单个CPU核上处理，这是一个简单而有效的选择
* 两阶段锁定: 数十年来，两阶段锁定一直是实现可序列化的标准方式，但是许多应用出于性能问题的考虑避免使用它
* **可串行化快照隔离(SSI)**



🤣以上是《设计数据密集型应用》读书笔记的第3部分，欢迎吐槽

