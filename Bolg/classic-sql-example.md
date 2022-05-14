# 好用的SQL范例

01-[delete-duplicate-emails](https://leetcode.com/problems/delete-duplicate-emails/)

```sql
-- method 1 
delete
from Person
where Id not in (
    select Id
    from (
             select min(Id) as Id
             from Person
             group by Email
         ) t
)
;
-- method 2 
delete
from Person
where id In
      (   select id
          from (   select email
                        , id
                        , row_number() over (partition by email order by id asc) as rank_id
                   from Person
               ) as c
          where c.rank_id > 1
      );
-- the following method doesn't work , you will get 
-- " You can't specify target table 'Person' for update in FROM clause"
delete
from Person
where Id not in (
    select min(Id) as Id
    from Person
    group by Email
);
```

02-[rising-temperature](https://leetcode.com/problems/rising-temperature/submissions/)

```sql
-- just a windows function , to avoid the every one or more day such as 2022-05-01 2022-05-04, we use timestampdiff function
select id
from (
         select id
              , temperature
              , recordDate
              , timestampdiff(day,lag(recordDate, 1) over (order by recordDate),recordDate) as date_diff
              , lag(temperature, 1) over (order by recordDate) yes_temperature

         from Weather
     ) t
where temperature > yes_temperature
and date_diff = 1
;
-- param2 - param1
select timestampdiff(day,'2022-05-04','2022-05-05') -- 1
```

03-[swap-salary](https://leetcode.com/problems/swap-salary/)

```sql
update Salary
set sex = if(sex = 'm','f','m');
```

04-[nth-highest-salary](https://leetcode.com/problems/nth-highest-salary/)

```sql
select max(salary)
from (
    select t1.salary
    from Employee t1
    inner join Employee t2
    on t1.salary <= t2.salary
    group by t1.salary
    having  count(distinct  t2.salary) = n
) t
; -- 这里使用了非等值连接，n=1 比自己(>=)的元素,只有自己，n=2,比自己(>=)的元素是自己和最大值；以此类推


select max(salary )
from (
    select id,salary 
         , dense_rank() over(order by salary  desc)  as rk
    from Employee
) t2
where t2.rk = n
```

05-[consecutive-numbers](https://leetcode.com/problems/consecutive-numbers/submissions/)

```sql
select distinct num as ConsecutiveNums
from (
         select  num
              , cast(id as signed) - cast(row_number() over(partition  by num order by id) as signed) as diff
         from Logs
     ) t
group by diff,num
having count(*) >=3;

-- mysql中
select cast(1 as signed)-(cast(-2 as unsigned));
-- 会引发：data truncation: BIGINT value is out of range in 的错误
```

06-[exchange-seats](https://leetcode.com/problems/exchange-seats/)

```sql
select case
           when mod(id, 2) != 0 and counts != id then id + 1
           when mod(id, 2) != 0 and counts = id then id
           else id - 1 end as id
     , student
from seat
   , ( select count(*) as counts
       from seat ) as seat_counts
order by id asc;
```

优先考虑偶数情况，然后再考虑奇数情况

07-[human-traffic-of-stadium/](https://leetcode.com/problems/human-traffic-of-stadium/submissions/)

```sql
with tmp1 as (
    select id
              , visit_date
              , people
              , id - row_number() over(order by id) as diff
    from stadium
    where people >=100
)
select id,visit_date ,people
from (
         select id
              , visit_date
              , people
              , diff
              , count(*) over( partition  by diff) as cnt
         from tmp1
     ) t
where t.cnt >=3
```

08-[中位数档次](https://www.nowcoder.com/practice/165d88474d434597bcd2af8bf72b24f1?tpId=82&tqId=37925&rp=1&ru=%2Fta%2Fsql&qru=%2Fta%2Fsql%2Fquestion-ranking)

```sql
select grade
from (
         select grade
              , ( select sum(number) from class_grade ) as total
              , sum(number) over (order by grade)          a
              , sum(number) over (order by grade desc)     b
         from class_grade
     ) t
where a >= total / 2
  and b >= total / 2
order by grade;
```

具体的图解如下图：

<img src="./img/cse/01.jpg" width = "100%" height = "30%" alt="图片名称" align=center />





