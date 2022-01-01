# impala

## 概览

Impala 提高了SQL查询性能的标准，同时保留了用户的使用体验。使用Impala，你可以使用，` SELECT, JOIN` 操作和各种聚合函数  查询存储在HDFS的数据、Kudu的数据 或者是HBASE的数据，并且可以快速的得到查询结果。



## MAX_ROW_SIZE

该参数保证impala 最小可以处理的行（超过该值更大的行可能会执行成功，但是不能保证），在构造结果集的中间行或者最终行的时候适用。在访问包含大字符串的列的时候，该参数可以防止 `out-of-memery`。

##### 类型 ： integer

##### 默认值：52488 （512kb)

##### 单位：

一个 numeric 参数表示 byte 大小，你也可以使用 `m` 或者 `mb` 来指定 兆比特，或者使用 `g` 或者 `gb` 来指定 吉比特，如果您指定了一个无法识别的格式，随后的查询将会报错。

##### 添加：CDH 5.13.0 / Impala 2.10.0

公司当前impala 和 cdh的版本：`impalad version 3.2.0-cdh6.3.2 RELEASE `

#### 用法笔记：

如果一个查询包含了太多列或者某字段有很长的 string 字段，可能会触发 `MAX_ROW_SIZE` 值而导致查询失败。增加 `MAX_ROW_SIZE`  设置以容纳存储在最大行的总字节数。对于失败的查询，可以通过日志来定位导致问题的行的大小。

实际上，impala会尝试处理超过 `MAX_ROW_SIZE` 的行，所以很多情况下，尽管有些行会超过 `MAX_ROW_SIZE` 设置的值，还是可以执行成功。配置一个比实际需要更大的值，可以为当前查询预留更多个内存来避免发生该异常。

在高并发的负载和查询大量数据的Hadoop集群中，传统的SQL语句建议最小化内存消耗是值得被提倡的。比如在一个查询中如果某个表有一个string 类型的字段，而且该字段的值是一个大 stirng ，那么你在使用 `select *` 的时候要确认你的结果集确实需要这个字段。

如果您打算 升级 CDH 5.12 / Impala 2.9 （或者更低的版本），按照下面这个教程 [Handling Large Rows During Upgrade to CDH 5.13 / Impala 2.10 or Higher](https://docs.cloudera.com/documentation/enterprise/6/6.3/topics/impala_upgrading.html#convert_read_size) ，检查您的查询是否收到这些更改的影响，如果是，请修改您的配置。这个建议对那些修改了`--read_size `默认值配置（默认值为8MB）的用户来说非常重要。

#### 例子

下面的例子中展示了如何设置 `MAX_ROW_SIZE` 的各种情形，首先我们创建了一个表，该表String字段有较大的值：

```sql 
create table big_strings (s1 string, s2 string, s3 string) stored as parquet;

set compression_codec=none;-- 关闭压缩，以便于更容易的通过 show table stats 定位数据量

set;-- 可以查看所有的配置项，及设置的值
...
MAX_ROW_SIZE	524288	REGULAR
...

-- 插入一个小的row 
insert into big_strings values ('one', 'two', 'three');

-- 插入一个在 MAX_ROW_SIZE 值 相近的行：1个 500KB 和 一个30KB的字符串
insert into big_strings values (repeat('12345',100000), 'short', repeat('123',10000));-- 12345 5个字节

-- 如下，如果一个查询同时实现s1和s3，则该行太大：
insert into big_strings values (repeat('12345',100000), 'short', repeat('12345',100000));
```

如果使用 `MAX_ROW_SIZE`  默认值，某列是否被materialized(物化) 将会影响当前查询成功与否。

```sql 
-- 所有s1的值会被 materialized 在 512KB MAX_ROW_SIZE buffer中
select count(distinct s1) from big_strings;
+--------------------+
| count(distinct s1) |
+--------------------+
| 2                  |
+--------------------+

-- 插入一个单个s1值就很大的行
insert into big_strings values (repeat('12345',1000000), 'short', repeat('12345',1000000));

select count(distinct(s1)) from big_strings;

-- 告警如下：
Row of size 4.77 MB could not be materialized in plan node with id 1. Increase the max_row_size query option (currently 512.00 KB) to process larger rows.
-- materialize 5MB 的数据太大了，需要设置更大的 max_row_size的值来处理更大的row 。

-- 如果查询需要更多的列，materialize 结果集需要更大的 max_row_size 值
select count(distinct s1, s2, s3) from big_strings;
-- 告警如下
Row of size 9.54 MB could not be materialized in plan node with id 1.
  Increase the max_row_size query option (currently 512.00 KB) to process larger rows.
  

-- 对于S2 这个短列来说，并没有什么影响：
select count(distinct(s2)) from big_strings;
+----------------------+
| count(distinct (s2)) |
+----------------------+
| 2                    |
+----------------------+

-- 不去 materialize 大列值的 query 也没有影响
select count(*) from big_strings;
+----------+
| count(*) |
+----------+
| 5        |
+----------+
```

下面的例子中展示了如何配置 MAX_ROW_SIZE 的值来保证对大 string 查询能够成功：

```sql 
-- 湿度提升 MAX_ROW_SIZE 的值来保证所有的 s1值 都可以 materialized
set max_row_size=7mb;

select count(distinct s1) from big_strings;
+--------------------+
| count(distinct s1) |
+--------------------+
| 3                  |
+--------------------+

-- 尽管设置了7MB，但如果结合s1,s2 形成的string 仍然是 很大的
select count(distinct s1, s2, s3) from big_strings;
WARNINGS: Row of size 9.54 MB could not be materialized in plan node with id 1. Increase the max_row_size query option (currently 7.00 MB) to process larger rows

-- 再次提升 MAX_ROW_SIZE 的值
set max_row_size=12mb;

select count(distinct s1, s2, s3) from big_strings;
+----------------------------+
| count(distinct s1, s2, s3) |
+----------------------------+
| 4                          |
+----------------------------+
```

下面的例子中展示了如何基于长值字段的特征来推断 MAX_ROW_SIZE 合适的值。

```sql
-- 通过检查表结构和列值来评估 MAX_ROW_SIZE 的限制值。
select max(length(s1) + length(s2) + length(s3)) / 1e6 as megabytes from big_strings;
+-----------+
| megabytes |
+-----------+
| 10.000005 |
+-----------+

-- 也可以使用下面的方式来进行计算
compute stats big_strings;
show column stats big_strings;
+--------+--------+------------------+--------+----------+-----------+
| Column | Type   | #Distinct Values | #Nulls | Max Size | Avg Size  |
+--------+--------+------------------+--------+----------+-----------+
| s1     | STRING | 2                | -1     | 5000000  | 2500002.5 |
| s2     | STRING | 2                | -1     | 10       | 7.5       |
| s3     | STRING | 2                | -1     | 5000000  | 2500005   |
+--------+--------+------------------+--------+----------+-----------+

-- 按照下面的估计，我们可以初步断定 MAX_ROW_SIZE 必须要 > 10.000005MB
```









































## 关于impala中 `Decimal expression overflowed` 问题

```sql 
-- 明确decimal(5,2) 表示什么？
-- 5：表示整数为和小数位 位数之和 等于 5
-- 2：表示小数位为2，如果数据不足2位，末尾补零。
-- 5-2=3：表示整数位的位数 应该等于3

-- 举个例子 1000.2 这个小数，如果执行 
select cast(1000.2 as decimal(5,2)) -- decimal expression overflowed   decimal_v2 环境下
select cast(1000.2 as decimal(5,2)) -- NULL  set decimal_v2 = false 环境下

-- '100.2'(注意这里是string)的 整数位=3，这个就是正常的 
select cast('100.2'  as decimal(5,2)); -- 100.20

--  下面的结果
SELECT cast(0 as decimal(18,3)) -- 0.000
```

**特别的**

```sql 
select  cast(0/0  as decimal(18,3)) --  decimal expression overflowed
select  cast(1/0  as decimal(18,3)) --  decimal expression overflowed

select  cast(1/0   as bigint);  -- 返回的结果 -9223372036854775808
select  cast(1/0   as bigint);  -- 返回的结果 -9223372036854775808
-- 原因 0/0 的结果为NaN,1/0 结果为Infinity ，行强转bigint 得到一样的结果 

-- 处理方式 使用 is_nan 和 is_inf 函数判断
select is_nan(0/0); -- true
select is_inf(1/0); -- true
select cast(if(is_nan(0/0),$x,$y) as decimal(18,3))

```









