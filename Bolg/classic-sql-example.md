# 好用的SQL范例

01-delete-duplicate-emails

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
      (
          select id
          from (
                   select email
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

02-rising-temperature

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

03-swap-salary

```sql
update Salary
set sex = if(sex = 'm','f','m');
```

04-nth-highest-salary

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
