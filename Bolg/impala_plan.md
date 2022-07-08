# impala execuiton plan

什么是MPP？



`exchange`的过程是如何进行的？







表结构及数据

```sql
drop table if exists  test.user;
create table if not exists test.user as
select 1 as user_id , 'z1' as user_name union all
select 2 as user_id , 'z2' as user_name union all
select 3 as user_id , 'z3' as user_name
;
drop table if exists test.goods;
create table if not exists test.goods as
select '001' as goods_id , '手机' as  goods_name union all
select '002' as goods_id , '洗衣机' as  goods_name union all
select '003' as goods_id , '冰箱' as  goods_name ;
;

drop table if exists test.order_tb;
create table if not exists test.order_tb as
select 1 as order_no , 1 as user_id , '001' as goods_id union all
select 2 as order_no , 1 as user_id , '002' as goods_id union all
select 3 as order_no , 2 as user_id , '002' as goods_id
;
```







关于为什么有时候执行`compute stats tb_name`时候，整体脚本更慢？
执行`explain`的时候即便没有表的统计信息也会生成一个执行计划，按照该执行计划执行得到耗时为time_A,若在`compute stats tb_name`的耗时为time_B，但是在执行`explain`之后得到了和之前一样的执行计划，此时整体消耗的时间为：$time\_A + time\_B$

:tipping_hand_man: 所以一个建议是在涉及大表的查询/关联时，不建议优先执行 `compute stats tb_name`







我们可以发现即便我们使用大表`left join`小表，impala一旦识别某个表是小表后，会立即将小表作为驱动表，基于小表构建Hash Table，然后大表做探测，从而完成哈希关联(转为`right join`)



| projection~投影~ pushdown | predicate~谓词~ pushdown | runtime filter             |
| ------------------------- | ------------------------ | -------------------------- |
| 列过滤                    | 行过滤(更底层的过滤方式) | 行过滤(运行加载到内存过滤) |

> 关于**predicate pushdow**和**runtime filter**的区别
>
> This optimization is called **filter pushdown** or **predicate pushdown** and aims at pushing down the filtering to the "bare metal", i.e. a data source engine. That is to increase the performance of queries since the filtering is performed at the very low level rather than dealing with the entire dataset after it has been loaded to Spark’s memory and perhaps causing memory issues.



















Reference

[^1]:https://conferences.oreilly.com/strata/strata-ca-2018/cdn.oreillystatic.com/en/assets/1/event/269/How%20to%20use%20Impala_s%20query%20plan%20and%20profile%20to%20fix%20performance%20issues%20Presentation.pdf



