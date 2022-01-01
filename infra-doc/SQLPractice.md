# SQL练习

> 本文档仅仅记录SQL练习的相关笔记。

## SQL的执行顺序：

1. from子句组装来自不同数据源的数据；
2. where子句基于指定的条件对记录行进行筛选；
3. group by子句将数据划分为多个分组；
4. 使用聚集函数进行计算；
5. 使用having子句筛选分组；
6. 计算所有的表达式；
7. select 的字段；
8. 使用order by对结果集进行排序

## SQL函数

[函数](<https://www.runoob.com/mysql/mysql-functions.html>)

下面是关于时间函数的练习题：

~~~sql  

~~~



~~~sql 
select dept_id,
sum(case sex when '男' then 1 else 0) male
sum(case sex when '女' then 1 else 0) female
from emp_sex
group by dept_id;
~~~





## 牛客网MySQL练习题

~~~SQL
--1) 获取最晚入职的员工
select * from employees order by hire_date desc limit 1;

--2) 查找入职员工时间排名倒数第三的员工的所有信息
select * from employees order by hire_date desc limit 2，1； -- 这里表示的是，从x行开始（不包含x行）开始的y行

--3) 查找各个部门当前（to_date='9999-01-01')领导当前薪水详情及其部门所对应的编号dept_no
select d.emp_no,d.dept_no,salary 
from  salaries s left join dept_manager 
d on s.emp_no = d.emp_no
where s.to_date = '9999-01-01'
and
d.to_date = '9999-01-01';

--4) 查找所有已经分配了部门的员工的last_name和first_name
select last_name,first_name 
from employees e inner join dept_emp d on 
e.emp_no = d.emp_no;
-- inner join的会过滤掉所有的null值，left join,也即左外连接，主表的null值也会被查询出来


--5）查找所有员工的last_name和first_name和对应部门的编号dept_no,也包括没有分配具体部门的员工
select e.last_name,e.first_name,d.dept_no 
from employees e left join dept_emp d on 
e.emp_no = d.emp_no;

--6)查找所有员工入职时候的薪水情况，给出emp_no和salary，并按照emp_no进行逆序
select s.emp_no,s.salary 
from employees e inner join salaries s
on e.emp_no = s.emp_no
where s.from_date = e.hire_date
order by s.emp_no desc;
 -- and 和 where 的区别：on 后面接and 表示的是连接的多层条件
 
 --7）查找薪水涨幅超过15次的员工emp_no和对应的次数t
select emp_no,t from (select emp_no,count(emp_no) t from salaries group by emp_no) tmp 
where t > 15;

select emp_no,coutn(*) t from salaries 
group by emp_no
hiving t > 15;

--8) 找出所有员工当前具体的薪资情况，对应相同的薪水只显示一次，并按照逆序显示
select distinct salary 
from salaries 
where to_date = '9999-01-01' 
order by  salary desc;

--9) 获取所有部门当前的manger的当前薪资，给出dept_no，emp_no , salary
select d.dept_no,d.emp_no,salary 
from dept_manager d left join salaries s
on d.emp_no = e.emp_no
where d.to_date = '9999-01-01' 
and s.to_date = '9999-01-01';

--10) 获取所有的非manager的员工的emp_no
select emp_no from employees
where emp_no not in(
select emp_no from dept_manager group by emp_no);

select distinct emp_no
from employees
where emp_no not in 
(select distinct emp_no
from dept_manager);

--11) 获取所有员工当前的manager，如果当前的manager是自己的话，不显示，结果第一列给出当前员工的emp_no，第二列给出manager对应的manger_no
select e.emp_no,m.emp_no as manager_no
from dept_emp as e left join dept_manager as m
on e.dept_no = m.dept_no
where e.to_date='9999-01-01'
and m.to_date='9999-01-01'
and e.emp_no != m.emp_no;


--12) 获取所有部门中当前薪水最高的相关信息，给出dept_no，emp_no和对应的salary
select dept_no,d.emp_no, max(salary) as salary
from dept_emp as d inner join salaries as s
on d.emp_no = s.emp_no
where s.to_date = '9999-01-01'
and d.to_date = '9999-01-01'
group by dept_no;

select d.dept_no, d.emp_no, max(salary) as ms
from dept_emp d left join salaries s 
on d.emp_no = s.emp_no
where d.to_date = '9999-01-01'
and s.to_date = '9999-01-01'
group by d.dept_no;

--13)从title表获取按照title进行分区，每个组个数大于等于2，给出title以及对应的数目t
select title ,count(*) t 
from titles 
group by title
having t >= 2;

--14) 从title表获取按照title进行分组，每组个数大于2，给出title和对应的数目t，对重复的title进行忽略
select title,count(distinct emp_no) t--这里是为了防止有重复的记录发生，所以对emp_no去重
from titles 
group by title
having t >= 2;


--15）查找employees表所有的emp_no为奇数，且last_name不为Mary的员工信息，并按照hire_date逆序排列。
select emp_no,birth_date,first_name,last_name,gender,hire_date
from employees
where (emp_no % 2) = 1
and last_name != 'Marry'
order by hire_date desc;

select * from employees
where last_name != 'Marry'
and emp_no % 2 = 1
order by hire_date desc;

--16) 统计出当前各个title类型对应的员工当前薪水对应的平均工资，结果给出title以及平均工资avg
select title,avg(salary) from titles t
left join salaries s 
on t.emp_no = s.emp_no
where s.to_date = '9999-01-01'
and t.to_date = '9999-01-01'
group by title;

--17) 获取当前薪水第二多的员工过的emp_no及其对应的薪水salary
select emp_no, salary 
from salaries
where to_date = '9999-01-01'
order by salary desc
limit 1,1;


--18) 查找当前薪水排名第二多的员工编号emp_no,薪水salary，last_name和first_name 不准使用order by
select emp_no,max(salary),last_name,first_name from
(select e.emp_no,salary,e.last_name,e.first_name
from  employees e left join salaries s 
on e.emp_no = s.emp_no
where salary < (select max(salary) from salaries))

--19) 查找所有员工的last_name和first_name以及对应的dept_name，也包括暂时没有分配部门的员工
select last_name,first_name,dept_name  
from employees e left join dept_emp d 
on e.emp_no = d.emp_no
left join departments m
on d.dept_no = m.dept_no;

--20) 查找员工编号为emp_no为10001，其自入职以来薪水的涨幅值growth

select (max(salary) - min(salary)) growth
from salaries
where from_date = (
select min(from_date)
from salaries where emp_no  = '10001'
)
or from_date = (
select max(from_date)
from salaries where emp_no  = '10001'
);

--下面的这种写法通不过测试，不知道为什么，是date类型，不能使用int么？
select (max(salary) - min(salary)) growth
from salaries
where from_date in(
select min(from_date),max(from_date) from salaries where emp_no = '10001');

--21)查找所有员工自从入职以的薪水涨幅情况，给出员工编号emp_no和对应的薪水涨幅growth,并按照growth进行升序
select e.emp_no (max(salary) - min(salary)) growth
from employees e left join salaries s
on e.emp_no = s.emp_no
group by e.emp.no
order by growth; -- 没有通过

select t1.emp_no,(t2.salary - t1.salary) as growth
from (select e.emp_no,salary
from employees e left join salaries s 
on e.emp_no = s.emp_no
where from_date = hire_date) t1 join -- 这里使用内连接，可能有些人刚来，没有确定薪水
(select e.emp_no,salary
from employees e left join salaries s 
on e.emp_no = s.emp_no
where to_date = '9999-01-01') t2
on t1.emp_no = t2.emp_no
order by growth;

--22）统计各个部门对应的员工涨幅的次数总和，给出部门编号dept_no，部门名称dept_name和次数sum
select d.dept_no,d.dept_name,sum from
departments d left join
(select dept_no,count(*) sum
from dept_emp e left join salaries s
on e.emp_no = s.emp_no
group by dept_no) t 
on d.dept_no = t.dept_no;

--23) 对所有员工的当前(to_date = '9999-01-01')薪水按照salary进行1—N的排名，相同salary并列且按照emp_no 升序。
select emp_no,salary,DENSE_RANK() over (order by salary desc) dense_rank
from salaries
where to_date = '9999-01-01'
order by salary desc, emp_no;

--24) 获取所有非manager员工当前的薪水情况，给出dept_no,emp_no，salary
select d.dept_no,e.emp_no,salary
from employees e left join dept_emp d
on e.emp_no = d.emp_no
left join salaries s 
on e.emp_no = s.emp_no 
left join dept_manager m 
on d.emp_no = m.emp_no
where m.emp_no is null 
and m.to_date = '9999-01-01'
and d.to_date = '9999-01-01'  -- 这样的写法有case没有通过



select d.dept_no,e.emp_no,salary 
from dept_emp as d left join employees as e 
on d.emp_no = e.emp_no
left join salaries as s
on e.emp_no = s.emp_no
where s.to_date = '9999-01-01' 
and e.emp_no not in (select emp_no 
from dept_manager
where to_date = '9999-01-01');

--25）获取员工其当前的薪水比其manager当前薪水还高的相关信息，结果第一列给出员工的emp_no，第二列给出其manager的manager_no，第三列给出该员工当前的薪水emp_salary,第四列给该员工对应的manager当前的薪水manager_salary





~~~

###  

##  HQL 练习，

这里存储有关于**HQL**的练习题：

### 数据仓库的SQL需求

#### 需求1：用户活跃主题

##### DWS：统计当日，当周，当月活动的每个设备明细

~~~sql
--1，每日活跃明细
select * 
from dwd_start_log
where dt = ''
group by mid_id;

--2，每周活跃明细
select * 
from dws_uv_detail_day
where dt >= date_add(next_day('','mo'),-7)
and dt <= date_add(next_day('','mo'),-1)
group by mid;

--3，每月活跃明细
select * 
from dwd_uv_detail_day
where dt >= date_add(next_day('','mo'),-7) -- 这个月的第一天怎么写？
and dt <= last_day('')
group by mid;


~~~

##### ADS：统计当日，当周，当月活动数量

~~~SQL

~~~



#### 需求2：用户新增

首次联网使用的用户，一个用户首次打开某个APP，那个这个用户定义为新增用户













~~~sql 
select dept_id,
sum(case sex when '男' then 1 else 0 end) male,
sum(case sex when '女' then 1 else 0 end) female
from emp_sex
group by dept_id;

select concat(constellation,",",blood_type),
concat_ws('|',collect_set(name))
from person_info
group by constellation,blood_type;


select movie,category_name
from movie_info lateral view explode(category) table_tmp as category_name;
~~~















