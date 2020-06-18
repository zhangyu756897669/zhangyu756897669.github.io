

# 技能-Hive-SQL学习

无标签

发布日期: 2020-06-18

---


# 数据库与表的增删改查

## 数据库

- create database-创建新数据库

```sql
--创建zhang数据库
create database zhang

 --制定数据库的位置
create database  if not exists zhang location '/zhang.db'
```

- alter database-修改数据库

```sql
---增加数据库属性
alter database zhang set dbproperties("CTtime"= "2020-05-01")
```

- drop database -删除数据库

```sql
---数据库下无表
drop database zhang;

---数据库下有表，-强制删除
drop database zhang cascade;
```

- 选择数据库

```sql
use android;
```

- 重置默认数据库

```sql
use default android;
```

- 查看所在的数据库

```sql
show databases;

--模糊查询
show databases like "s%"

--- 查询数据库信息
desc database  zhang;
7
---查询数据库扩展属性
desc database extended zhang;
```

## 数据表

- create table-创建新表

```sql
create [external] table [if not exists] table_name [(col_name data_type [comment col_comment],....)][partitioned by (col_name data_type [comment col_comment],..)][clustered by (col_name, col_name, ...)][row format row_format][stored as file_format][location hdfs_path]

--- create table 创建指定名称的表，如果相同名称的表已存在，则用if not exists 选项来忽略这个异常。--extername 关键字让用户创建一个外部表---partitioned by  分区：分目录，把一个大的数据集根据业务需要分割成小的数据集。在查询时通过where子句的表达式来选择查询所需的指定分区，提高查询效率。
--clustered by 分桶--row format  字段之间的分隔符---stored as 文件存储格式--location， 指定表在HDFS上的存储位置---在zhang库中创建test表create external table dept(    deptid int,     dname string,     loc int) row format delimited fields terminated by '\t'
```

- update-更新数据库中的数据

```sql
update 表名set  需更新的列名1= 新值1, 需更新的列名2=新值2,...where  列名 = 某个原有的值

UPDATE Websites SET alexa='5000', country='USA'

 --更新的数据WHERE name='菜鸟教程';
```

- insert into() -向数据库中插入新数据

```sql
insert into 表名（列名1,列名2,列名3...)values(值1, 值2, 值3...)
```

- delete-从数据库中删除数据

```sql
delete from 表名where column = value...DELETE FROM WebsitesWHERE name='Facebook' AND country='USA';--删除表中所有的行，表结果不变delete from table_name;
```

- alter table- 修改数据库表

```sql
--清楚表中数据,删除掉指定分区

ALTER TABLE shphonefeature DROP IF EXISTS PARTITION(year = 2015, month = 10, day = 1);

---lter table test.mon_mau_list drop partition (hit_mon = '{0}')
```

- drop table - 删除表
- create index -创建索引
- drop index -删除索引
- refresh table 表名 - 刷新数据表

```sql
refresh table computer_log.client_ios_log
```

- 查看当前使用的数据库中有哪些表

```sql
show tables;
```

- 查看非当前使用的数据库中有哪些表

```sql
show tables in myhive;
```

- 查看数据库中以 android 开头的表

```sql
use android;show tables like 'android*'
```

- 查看表的详细信息

```sql
desc formatted android
```

- 查询分区表有多少分区

```sql
show partitions dept_partition;
```

- 查看分区表结果

```sql
desc formatted dept_partition
```

- 增加分区

```sql
alter table dept_partition add partition(month='201705') partition(month='201704')
```

- 删除分区

```sql
alter table dept_partition drop partition (month='201705')
```

## 内部表与外部表

- 内部表(管理表)：默认创建内部表， 删除表会删除所有数据
- 外部表： 删除表不会删除这份数据，不过描述表的元数据信息会被删除掉。
- 原始日志数据应该建立外部表（避免误删）， 用到的中间表、结果表使用内部表存储。
- 查看表是内部表还是外部表

```sql
--查看表信息
desc  formatted  table_name
```

- 内部表与外部表的相互转换

```sql
---内部表转换为外部表
alter table student set tblproperties('EXTERNAL' = 'true') ---单引号、大小写不能变

---外部表转化为内部表
alter table student set tbproperties('EXTERNAL' = 'false')
```

# 查询

## select…from…

- 加入表中一列含有多个元素， 我们可以只查找此列的第一个元素

```sql
select name, subord[0] from employees;
```

- 可以使用 “点” 符号， 类似：表的别名 . 列名 这样的用法

```sql
select  name, address.city  from  employees;
```

- 使用正则表达式，可以选出所有列名以 price 作为前缀的列

```sql
select  'price.*'  from  stocks;
```

- 使用列值进行计算

```sql
select count(distinct account), avg(salary) from employees;
```

- 使用别名

```sql
select  count(distinct acount) as uv   from   employees;
```

- 如果用 distinct, select 后面必须直接跟 distinct

```sql
select  distinct user_account, province from    computer_viedata

```

## where

- 关系型运算符优先级高到低为：not - and - or

```sql
select * from employees where country = 'us' and state = 'ca';select * from employees where country  not in ('us', 'china')
```

- 数学运算符与关系运算符

[Untitled](https://www.notion.so/055703aab4474fd18a54d7bb0f0bd42d)

- like、rlike

```sql
---like、 rlike 
select name, address.street from employees where address.street rlike '.*(beijing|shanghai).*';

select name, address.street from employeeswhere address.street like '%beijing%' or address.street like '%shanghai%';
```

[Untitled](https://www.notion.so/18d9ebe5a2be4260a857afd2a13a041f)

## group by

```sql
--- 对结果进行分类

select     year(ymd), avg(price_close) 

from     stockswhere     exchange = 'nasdaq' and symbol = 'aapl'

group by     year(ymd)

order by     year(ymd) desc;  --desc 从高到低排列
```

## order by

```sql
--对查询的所有结果进行排序, 可在字段加 DESC 关键字， 进行降序排序。 （默认 ASC， 升序）

select     year(ymd), avg(price_close) 

from     stockswhere     exchange = 'nasdaq' and symbol = 'aapl'

group by     year(ymd)

order by     year(ymd) desc;
```

```sql
--先对code进行排序，然后对code里的姓名进行排序

select * from a 

order by code, name desc;
```

## having

```sql
--- having 子句来限制输出结果--- 查找平均工资大于3000的部门

select     deparment, avg(salary) as average from      salary_info group by     deparment having     average > 3000
```

- having 与 where 的区别：
- Where 是一个约束声明，使用Where约束来自数据库的数据，Where是在结果返回之前起作用的，Where中不能使用聚合函数。
- Having是一个过滤声明，是在查询返回结果集以后对查询结果进行的过滤操作，在Having中可以使用聚合函数。

## limit

```sql
---使用limit语句限制返回的行数，只显示 10 行

select count(distinct account) as uv  from  employees  

limit 10;
```

## 表连接

### join

Hive中Join的关联键必须在ON ()中指定，不能在Where中指定,ON 子句指定了两个表间数据进行连接的条件。

![https://i.loli.net/2019/06/11/5cffb911ad8e183153.png](https://i.loli.net/2019/06/11/5cffb911ad8e183153.png)

- 对于多张表进行连接查询

```sql
---为什么条件内不将表 b 和表 c 进行连接操作， 因为 Hive总是按照从左到右的顺序来执行

SELECT
    *
FROM
    (((a JOIN b ON a.ymd = b.ymd)
      JOIN c ON a.ymd = c.ymd)
      join d on a.ymd = d.ymd)
WHERE 
    a. symbol = 'Apple'  AND b.symbol = 'Ibm' AND c.symbol = 'Google'
```

### 并集：union 与 union all

```sql
with a1 as (
        select
            user_account
        from
            data
        where
            hit_date between '2018-12-01' and '2018-12-02'
            and
            nbtn_name like "%支付宝%"
        union 
        select
            user_account
        from
            data
        where
            hit_date between '2018-12-01' and '2018-12-02'
        and
        nbtn_name like "%手淘%")
select
    count(user_account) as pv
from
    a1
```

- union 与 union all 的不同：
- union, 结果包含所有行， 并删除重复行
- unoin all, 结果包含所有行， 但不删除重复行

### 交集：intersect

```sql
with a1 as (
        select
            user_account
        from
            data
        where
            hit_date between '2018-12-01' and '2018-12-02'
            and
            nbtn_name like "%支付宝%"
        intersect
        select
            user_account
        from
            data
        where
            hit_date between '2018-12-01' and '2018-12-02'
        and
        nbtn_name like "%手淘%")
select
    count(user_account) as pv
from
    a1
```

### 差集：except

```sql
with a1 as (
        select
            user_account
        from
            data
        where
            hit_date between '2018-12-01' and '2018-12-25'
            and
            nbtn_name like "%支付宝%"
        except
        select
            user_account
        from
            data
        where
            hit_date between '2018-12-01' and '2018-12-25'
        and
        nbtn_name like "%手淘%")
select
    count(user_account) as pv
from
    a1
```

# 函数

## 聚合函数

[Untitled](https://www.notion.so/95e5f3e703a74864afad9be85a77035a)

- count(1)、count(*)、count(column) 之间的区别
- 执行范围上： count(*) 和 count (1)  都包含了 对NULL的统计。 count(列名)统计时不包含NULL值。
- 执行速度上： 列名为主键时， count(列名) 最快*。  当无主键时， count(1) 最快。  当表只有一个字段，count(*) 最快。*

---

## 时间函数

[Untitled](https://www.notion.so/14a471014a4b4f6995852df51f317e2b)

### 求留存率

- datediff-求留存率

```sql
---一次性求次1日，次3日， 次7日留存，此方法不能计算pv，会造成笛卡尔积
with a1 as (select 
    hit_date,
    user_account
from 
    computer_view.data
where 
    hit_date between '2019-04-25' and '2019-05-13'
    and 
    btn_information is not null),
a2 as (select 
    hit_date,
    user_account
from 
    computer_view.data
where 
    hit_date between '2019-04-25' and '2019-05-13'
    and 
    btn_information is not null)
select 
a1.hit_date,
count(distinct a1.user_account) uv,
count(distinct case when datediff(a2.hit_date, a1.hit_date) = 1 then a1.user_account else null end ) next_day,
count(distinct case when datediff(a2.hit_date, a1.hit_date) = 3 then a1.user_account else null end ) three_day,
count(distinct case when datediff(a2.hit_date, a1.hit_date) = 7 then a1.user_account else null end ) seven_day
from a1 join a2 on a1.user_account = a2.user_account
group by a1.hit_date
order by a1.hit_date
limit 100
```

- date_add 求留存率

```sql
---步骤1：统计每天的uv

---步骤2： - 统计10-15号每天的次日留存数， 统计次3、7日留存只需将1换为3、7
with a1 as (
    select 
        user_account,
        hit_date
    from 
        computer_view.data
    where 
        hit_date between  '2018-11-10' and '2018-11-15'
),
a2 as (
        select 
        user_account,
        hit_date
    from 
        computer_view.data
    where 
        hit_date between '2018-11-10' and '2018-11-25'
)
select 
    a1.hit_date,
    count(distinct a1.user_account) as uv
from
    a1 join a2 on a1.user_account = a2.user_account
WHERE   
    a2.hit_date =  date_add(a1.hit_date, 1) 
group by 
    a1.hit_date
order BY
    a1.hit_date

--步骤3：计算留存率
```

- 计算留存率的其他写法-迷神

```sql
-- 留存sql优化
select count(1)
from(
    select userid, count(1)
    from(
        select t1.userid,
                t1.statdate
        from
            table1 t1
        where
            t1.statdate = ${上30天日期}
            and t1.statdate <= ${上一天日期}
        group by
            t1.userid,
            t1.statdate
        ) s1
    group by
        userid
    having
        count(1)  2
    ) R1

--此sql为一个样例，计算连续跟任意都适用，至于计算第N天，只需要更改下日期过滤条件，变成=$[上N天日期]，=${上一天日期}。 
--另外，这种方式适合跑当前周期数据，如果跑历史数据，可以写个循环。当然，最暴力还是直接用userid 关联。

--这种写法，更多是针对现在大部分分布式处理平台的特性，尽可能将数据合理均匀分片，每台服务器各自运算自己的，最后汇总。 尽可能少用 count distinct 这种写法，因为无法利用分片的特性。
```

- 留存率的另一种写法-勇哥

```sql
with a1 as (
select
	hit_date,
	user_account,
	count(1) as hit_count
from 
	apache_computer_view.client_android_log
WHERE 
	hit_date between '2020-04-01' and  '2020-04-07'
	and
	btn_navigation  like "%查询办理%"
group by
    1,2
),
a2 as (
select
	hit_date,
    user_account,
    count(1) as hit_count
from 
	apache_computer_view.client_android_log
WHERE
	hit_date between '2020-04-01' and  '2020-04-07'
	and
	btn_navigation  like "%查询办理%"
group by
    1,2
)
select 
	a1.hit_date as one,
	a2.hit_date as two,
	datediff(a2.hit_date, a1.hit_date) as cha,
	count(distinct a2.user_account),
	sum(a2.hit_count)
from
    a1 left join a2 on a1.user_account = a2.user_account
group by
    1,2
having
    cha > 0
order by
    1,2
```

- 计算月留存率的简单写法：筛选出在两个月份出现的用户

```sql
with a1 as (select 
    user_account,
    count(distinct month (hit_date)) as c
from 
    apache_computer_view.client_android_log 
where 
     hit_date between '2019-03-01' and '2019-04-31'
group by 
 user_account
having 
  c = 2
union 
select 
    user_account,
    count(distinct month (hit_date)) as c
from 
    apache_computer_view.client_ios_log 
where 
     hit_date between '2019-03-01' and '2019-04-31'
group by 
 user_account
having 
  c = 2
    )
select 
count(distinct user_account) as uv 
from 
a1
```

## 条件判断：case when 与 if

1. **IF( expr , v1 , v2 )函数**
- 查出班级所有学生，如果年龄小于20，就标准为少年，否则标记为青年。

```sql
select 
    * 
     if(age<20,'少年','青年') AS ifage 
from  
    student
```

1. **ifnull(V1,V2)函数**
- 如果v1不为空，则直接返回v1;如果v1为空，则返回参数v2

```sql
select
    ifnull(1,2),
    ifnull(null,10);
```

1. **case when 函数**
- 对不同字母进行省份转换

```sql
select
case 
        when province like 'ah' then '安徽'
        when province like 'fj' then '福建'
        when province like 'gd' then '广东'
    else 'm' 
    end as province ,
    count(distinct user_account) uv,
    count(page_name) pv
from android_log
where hit_date between '{}' and '{}'
and page_name like '%Kefujh%'
group by case 
        when province like 'ah' then '安徽'
        when province like 'fj' then '福建'
        when province like 'gd' then '广东'
    else 'm' 
    end 
order by case 
        when province like 'ah' then '安徽'
        when province like 'fj' then '福建'
        when province like 'gd' then '广东'
    else 'm' 
    end 
limit 1000
```

- 统计各部门男女分别有多少人

[Untitled](https://www.notion.so/9b866f26284c4b7ba4592497f75c1c75)

```sql
select 
'部门',
sum( case when 性别 = '男' then 1  else null end ) as '男',
sum( case when 性别 = '女' then 1 else null end ) as  '女'
from 
table 
group by 
'部门'
```

- 范围转换

```sql
select 
case when population < 250 then '1' 
			when population  = 250 and  population < 500 then '2'
else null end  as pop_class,
count(*) as cntfrom 
from pop 
```

```sql
select 
 case when dt = 3 then '第一季度' 
    when  dt = 6 then  '第二季度'  
    when dt in (1,2,4,5)  then '其他' 
    else null 
    end as dt ,
sales,
sum(sales) over (order by dt  rows between 2 preceding and current row   )  as a1
from 
(
select 
count(  user_account) as sales,
month(hit_date)  as dt 
from 
apache_computer_view.client_android_log 
where 
hit_date between '2019-01-01' and '2019-06-31'
group by 
month(hit_date ))
group by 
 case when dt = 3 then '第一季度' 
    when  dt = 6 then  '第二季度'  
    when dt in (1,2,4,5)  then '其他' 
    else null 
    end,sales
```

---

## 行转列

1. 函数
- **case when**
- **concat**(字符1, 字符2…) ：函数在连接字符串时，只要其中一个是NUll，则返回NUll
- **concat_ws**(分隔符, 字符1, 字符2,…): 函数需要指定分隔符，只能接收 string或string类型的数组，只要有一个字符串不是NUll， 则不会返回NULL。
- **collect_set**(col): 函数值接受基本数据类型，主要作用是将某字段的值进行去重汇总，产生array类型字段。
1. 案例
- 多行转多列

[Untitled](https://www.notion.so/3be2808d46744e2b917a69f302de1ebd)

查询结果如下：

[Untitled](https://www.notion.so/ed9dc647bf534c56aee4f6eed8c3db34)

```sql
select 
	年,
sum( case when  季度 = 1 then 销售量  else null end ) 一季度,
sum( case when 季度 = 2 then 销售量 else null end ) as 二季度,
sum( case when 季度 = 3 then 销售量 else null end ) as  三季度,
sum( case when 季度 = 4 then 销售量 else null end ) as 四季度
from 
page 
group by 
年
order by 
年
```

- 多行转单列

[Untitled](https://www.notion.so/31f0fa2500fa437bb9ac50fe00486aec)

```sql
select 
	concat_ws( '-', province, city) ,
	count( distinct user_account) as uv 
from 
	(select 
		province,
		city,
user_account
	from 
		table
where 
hit_date = '2020-05-06'
	)
group by 
	concat_ws( '-', province, city) 
order by 
		uv DESC 
```

- 多行转单列-复杂

[Untitled](https://www.notion.so/85784ade5eb74ae4bf489346487a3d06)

把星座和血型一样的人归类到一起：

[Untitled](https://www.notion.so/c5a39428e3a844b7a8e3c51a6ad89e28)

```sql
select 
con_blood,
concat_ws ("\", collect_set(name))
from 
(SELECT 
concat_ws( ',', contellation, blood_type) as con_blood,
name
from 
table1) 
group by 
con_blood
```

- 求将每个省的城市列出来

```
---辽宁省  ["营口市","大连","大连市","抚顺市","铁岭","盘锦","锦州","沈阳市","辽阳","鞍山","铁岭市","本溪市","丹东市","丹东","沈阳","朝阳市","锦州市","辽阳市","阜新市","鞍山市","盘锦市","葫芦岛","营口","抚顺","葫芦岛市","阜新","本溪","朝阳"]
```

```sql
select 
province, 
collect_set(city) 
from 
android 
where  dt = '2020-05-01' and city is not null 
group by 
province 

---辽宁省  ["营口市","大连","大连市",.....,"朝阳"]
```

- 求出一个月内活跃天数大于20天的用户数

```sql
select 
count(distinct user_account) as uv 
from 
(select 
user_account,
collect_set(hit_date)  as dt
from 
an
where 
hit_date between '2020-05-01' and '2020-05-31'
group by 
user_account
having
size(dt) > 20)
```

---

- 两表结合

[user_basic_info](https://www.notion.so/030e6cc4100746f78cd14d8c97988c3c)

[user_address](https://www.notion.so/10c03ebfa15447d0b910c5c797835bbd)

我们可以看到同一个用户不止一个地址，我们需要把数据变为如下格式

[user_info](https://www.notion.so/d59de578954844cd81b9579a26ae3e07)

```sql
  with a1as (select 
name,
concat_ws (',', collect_set(address)) as addres
from 
user_address
group by 
name),
a2 as (
select 
id, name 
from 
user_basic_info ) 
select 
a2.id, a2.name ,a1.addres
from 
a2 left join a1 on a2.name = a1.name 

select

 a1.id, a1.name, concat_ws(',', collect_set(a2.address)) as address

from

user_basic_info a1 join user_address a2  on a1.name=a2.name

group by a1.id, a1.name

```

将组合的表拆分成如下形式：

[Untitled](https://www.notion.so/0651c21f509149a59e33ffef2733053c)

```sql
--这样写会会生成笛卡尔积，执行报错
select id， name, explode(address)  as address from user_info;

---需这样写

select 
id, name,  add 
from 
user_info a1
lateral view explode(a1.address) adtable as add ;
```

## 列转行

1. 函数
- explode(col): 将hive列中复杂的array或者map结构拆分成多行
- **lateral view** 用法： lateral view UDTF(expression) adtable  as a1  说明： 用户和split,explode 等UDTF一起使用，能够将一列数据拆分成多行数据， 在此基础上可以对拆分的数据进行聚合计算. 形成一个新的表，并对原来的表进行侧写
1. 案例
- 需求1

[Untitled](https://www.notion.so/0fb30bcbf41d4f0d849761ad44b8e5d2)

要求：

[Untitled](https://www.notion.so/482c74f2e4974c17a778347724389337)

```sql
select 
	movie. categrory_name
from 
movie_info
lateral  view explode ( categrory)  adtable as categrory_name
```

- 需求2： 将 表 table 中的 `adid_list` 转换为单独的行。

表-table：

[Untitled](https://www.notion.so/0c398ee564d14e8b924600b5edc6c2cd)

输出结果为：

[Untitled](https://www.notion.so/9b012931a2944f14bd31f810a67ee57e)

```sql
select 
	pageid, adid
from 
	talbe1
lateral view explode(adid_list) adtable as adid
```

- 需求3： 计算特定广告的展现次数

```sql
select 
	adid, 
	count(1) 
from 
 table
later view explode(adid_list) adtable as  adid
group by 
adid
```

输出结果为：

[Untitled](https://www.notion.so/d99abba5c5c9462e930a080d6201e3ac)

- 需求4： 多个 lateral view 查询

表： table2

[Untitled](https://www.notion.so/10fcc80c3f164561bb10c9661e298713)

输出结果为：

[Untitled](https://www.notion.so/cd2500e08bce45ce85967c0724d6cb8a)

```sql
select 
	mycol1,
	mycol2
from 
	table1
alteral  view explode(array) adtable as mycol1 
alteral  view explode(col2)  adtable as mycol2
```

---

## 窗口函数

1. 函数

[Untitled](https://www.notion.so/1d4b120e53004ce598cb867350a8391a)

1. 案例

[business](https://www.notion.so/b1f5181119e448ecbb1091f200d1df47)

- 查询在2017年4月购买的顾客及总人数

```sql
select 
	name,
count( *)over()
from 
business
where 
substring(hit_date, 1,7) = '2017-04'
group by 
name 
```

[Untitled](https://www.notion.so/f01b633de76944dcaef4092b1ec908f7)

- 查询顾客的购买明细及月购买总额

```sql
select 
*,
sum(cost)over(distribute by month(orderdate))
--sum(cost)over(partition by month(orderdate))
from 
business
```

- 要将cost按照日期进行累加

```sql
select 
	*,
	sum(cost)over( order by orderdate   rows between unbounded preceding and  current row)
--先排序，之后按照顺序，从起到到当前行进行求和
--sum没有问题，但是count(distinct user_account) 就不能用这种方法
from 
	business
```

- 按照日期进行排序，并将当前日期和前一天、后一天数据求和

```sql
select 
*,
sum(cost) over(order by  orderdate  rows between 1   preceding  between  1 following ) 
from business  
```

- 求每个人将按照日期进行累加的消费金额

```sql
select 
name, 
sum(cost) over ( partition by name order by  orderdate  row between unbounded preceding and current row) 
from  business 
group by 
name 
```

- 要将cost按照日期进行倒序累加

```sql
 select 
*,
sum(cost) over ( order by orderdate desc row between unbounded preceding and current row)
from 
business 
```

- 查询顾客的上次购买时间

```sql
select 
*，
lag(orderdate,1)over( partation by name order by  orderdate)
from 
business
```

- 查询顾客上次购买的时间, 与下次购买时间。相邻两个时间戳如何相减，求时间

```sql
select 
*,
lag(orderdate, 1) over (partition by name order by orderdate) as up_date,
lead(orderdate, 1) over (partition by name order by orderdate) as downdate
from 
business 
```

- 查询前20%时间的订单信息

```sql
select 
* 
from 
	(select 
		name,
		orderdate,
		cost,
		ntile(5)over(order by orderdate) as git
		from 
		business)
where 
git = 1
```

- **[ntile函数详解](https://www.cnblogs.com/52xf/p/4209211.html)** ntile函数可以将有序数据，根据指定的组数进行分组处理。 编号从1开始，对于每一行，ntile将返回此行所属的组编号。 ntile函数的分组依据：
- 每组包含的数据个数不能大于它上一组 包含的数据个数
- 计算规则：1. 检查能不能对所有满足条件的记录进行平均分组，若能则直接平均分配完成分组。2. 若不能，则会先分出一个组，此组个数为（总个数/总组数）+1。3. 分配之后系统会继续比较余下的记录数与未分配的组数能不能进行平均分配，若不能，则根据上面条件再分配。
- 例如：将6个记录分为4组， 不能平均分配则，第一组记录数为 （6/4)+1 = 2条记录。剩余4条记录分为3组，不能平均分配，则第二组记录数为（4/3)+1=2条记录。剩余2条记录分为2组，则剩余2组各1条记录。

## 排序函数

1. 函数

SQl 中用于排序的函数有：rank、dense_rank、row_number、ntile函数,其语法为：

```sql
rank() over ( partition by A ORDER BY B  desc )   ---1、1、3
dense_rank() over (partition by A order by B desc)  --- 1、1、2
row_number() over (partition by A ORDER BY b desc)    --1、2、3
ntile() over (partition by A ORDER BY b desc)      --分组
```

- 如何找出各省点击人数Top10的按钮？

对于这个问题，首先要理清自己的思路：1. 取出 省份、按钮和 uv;2. 各省分组内，按照uv进行从大到小排序，并输出一列排序序号;3. 根据排序序号，取出排序前10的按钮和省份。 

```sql
select 
province,
nbtn_name
from 
(
select 
    province,  --省份
    nbtn_name, --按钮 
    uv,        --uv
    dense_rank()over(partition by province order by uv  DESC) as ran --排序
from 
(select
    province,
    nbtn_name,
    count(distinct user_account) as uv
from 
table 
where 
    nbtn_name is not null  
    and 
    hit_date = '2020-06-01'
group by 
    province,
    nbtn_name))
where 
ran between '1' and '10'
```

- 求连续3个月活跃的用户数

```sql
select 
count(distinct user_account) as uv 
from 
(
select 
dt,
user_account,
dense_rank()over(partition by user_account order by dt) as raws
from 
(

(select 
user_account,
month(hit_date)  as dt 
from 
table1
where 
hit_date between '2020-04-01' and '2020-06-31'
group by 
user_account, dt) a1
) a2
)
where 
a2.raws = 3
```

- 求4月连续7天进行签到的用户数

```sql
----1. 求出手机号和日期，并去重
----2. 根据手机号，对日期进行排序，并且日期和排序进行相减
----3. 对相减后得到的日期进行统计，并计算数量大于7的用户
----4. 对数量大于7的用户进行去重处理

select 
count(distinct user_account) as uv 
from 
(
select 
	user_account,
	raw,
	count(raw) as raw_1
from 
(
		select 
			user_account,
				hit_date,
			date_sub(hit_date, row_number()over(partition by user_account order by hit_date) )as raw
	from 
		(
		select
			user_account,
			hit_date
		from 
			apache_computer_view.client_android_log
		where 
			hit_date between '2020-04-01' and '2020-04-31'
		group by 
			user_account,
			hit_date))
group by 
	user_account,
	raw)
where 
raw_1 >= 7
```

---

## 字符串函数

1. 函数

[Untitled](https://www.notion.so/5ddc2ea0afb142c8816728fe78c521a7)

1. 函数详解

**substr函数与 substring函数用法相同:**

```
substr/substring( A, k开始截取的位置，截取长度)
```

举例：

```
substr(string,4): 从右第4位置截取到最后，结果为：ing
substr(string,1,3):取左边第1位置起，3字长的字符串，结果为：str
substr(string,-3,3):取右边第1位置起，3字长的字符串,右边第一位置往右不够3字长，结果为：g
substr(string,-3,3):取右边第1位置起，3字长的字符串，结果为：ing
```

**substring_index函数**

```
substring_index(A, 分割的字符,截取字符的位置)
```

举例：

```
substring_index('15,151,152,16',',',1)：取第一个逗号前面的字符串，结果为：15

substring_index('15,151,152,16',',',2)：取第二个逗号前面部分，结果为：15,151

substring_index('15,151,152,16',',',-1)：取目标字符串中最后一个含 “,” 位子的后的部分，结果为：16

substring_index(substring_index('15,151,152,16',',',2),',',-1):取第二个逗号前面部分,然后最后逗号的前面部分，结果为：151

substring_index(substring_index('15,151,152,16',',',-2),',',1)：取倒数第二个逗号后面部分字符串，再去这部分里第一个都号前的部分，结果为：152

```

**split函数**

```
split(A, 分割的字符) 
```

举例：

```
split('a,b,c,d',','):根据逗号进行分割，结果为： ["a","b","c","d"]

split('a,b,c,d',',')[0]： 取结果数组中的某一项，结果为： a

split('192.168.0.1','\\.')： 点号这种特殊字符的时候需要做特殊的处理，结果为：["192","168","0","1"]

"....  split('192.168.0.1','\\\\.') ... ": split包含在 "" 之中时 需要加4个\,不然得到的值是null

同样的 | 等特殊符号也需要做类似 处理。

```

3.区分函数之前的区别 * substr函数与 substring函数是根据截取的位置来进行分割。 * substring_index和split是根据特定的字符来进行分割。

1. 需求
- 将一些字段拆解出来进行使用，比如：Syjh-sjsy-zygn-3_1字段，我们只需要Syjh-sjsy-zygn位置的所有按钮。

```
select 
    substring_index(nbtn_position, '-',3) as position,
    count(distinct user_account) as uv 
from 
    apache_computer_view
where 
    hit_date = '2020-03-01'
    and 
    nbtn_position like '%Syjh%'
group by 
    substring_index(nbtn_position, '-',3)

```

---

- 左填充与右填充

**LPAD(str,len,padstr)**

参数意义：LPAD(要查询的字段，长度，用来填充的字段)，**lpad是在左边填充，rpad是在右边填充**

案 例：如下图，在查询的frname结果上用我自定义的字符串补充到固定长度。

```sql
select 
	lpad(nbtn_name, 7, 'xo') as '左填充',
	rpad(nbtn_name, 7, 'xo') as '右填充',
	nbtn_name as '不填充'
from 
	table1
```

[Untitled](https://www.notion.so/da66b81bec7f4641b1684963d33912bd)

## group by 中rollup、cube、grouping、grouping sets用法

1. 需求背景： 通过 a1 明细表，获得每个店铺，每个城市，每个省份，每个大区以及全国5月的份的成交量情况。

[table1](https://www.notion.so/3a902118b6364a518c00af03d0876c6f)

2. 解法

解法1：分别写5个sql，这种方法太低效了， 还需要在excel中进行合并。

```sql
---全国成交量
select count(order_id)  as sales from table1 
--大区成交量
select area, count(order_id) as sales from table1 group by area
--省成交量
select area, province, count(order_id) as sales from table1 group by area, province
---城市成交量
select area, province, city, count(order_id) as sales from table1 group by  area, province, city
--店铺成交量
select area, province, city, shop, count(order_id) as sales from table1 group by area, province, city, shop 

```

解法2：通过 union 和 union all 对查询结果进行纵向合并

```sql
---全国成交量
select null, null, null, null, null, count(order_id)  as sales from table1 
union all 

--大区成交量
select null, null, null,null,  area, count(order_id) as sales from table1 group by area
union all 

--省成交量
select null, null,null,  area, province, count(order_id) as sales from table1 group by area, province

union all 
---城市成交量
select null, null, area, province, city, count(order_id) as sales from table1 group by  area, province, city

union all
--店铺成交量
select null,  area, province, city, shop, count(order_id) as sales from table1 group by area, province, city, shop 

---上述式中有很多 null, 这是因为 union all 拼接的两个表的列数需要相等。
```

结果如下：

![https://i.loli.net/2019/06/10/5cfe6c219ca3f57084.png](https://i.loli.net/2019/06/10/5cfe6c219ca3f57084.png)

解法3：用`grouping sets`来根据不同维度组合进行聚合

```sql
select 
	null, area, province, city, shop
from 
	table1
group by 
	null, area, province, city, shop
grouping set
	( null, (null, area), (null, area,province), (null, area, province, city), (null,area, province, city,shop) )
```

得到结果与利用 `union all`拼接结果相同。`group by`后面的字段表示要分组聚合的全部字段， `grouping sets`后面为 `group by` 后面各种字段的组合。

解法4：`cube`函数， 对`group by`的维度的所有组合进行聚合。

```sql
select 
	 area, province, city, shop
from 
	table1
group by 
	 area, province, city, shop
with cube
```

`cube` 会先对全部数据进行聚合，即 `null,null,null,null` 进行聚合，(只是不像解法3一样，显示null列， 如需显示只要加入null即可） 然后对 `area, null, null, null` 进行聚合，.......

解法5：`rollup`函数

```sql
select 
	 area, province, city, shop
from 
	table1
group by 
	 area, province, city, shop
with rollup
```

`rollup` 函数输出结果，不会有 `null, null, null, city` 值， 和 `cube` 的区别在于： `cube` 是维度更细的统计，假设数据有 `n` 个维度， 那么 `rollup` 会有 `n` 个聚合，`cube` 会有 `2n` 个聚合。

## 空字段赋值

NVL：给值为NULL的数据赋值，格式为NVL（string1, replace_with),功能为：如果string1为null，则NVL函数返回replace_with的值，否则返回string1的值，如果两个参数都为NULL，则返回NULL。

```
select nvl(comm, -1) from student;
```

## 查看系统内置函数

1. 查看系统自带的函数

```
show functions
```

1. 显示自带的函数的用户

```
desc function 函数名;
```

1. 详细显示自带的函数用法

```
desc function extended 函数名
```

# Hive避免数据倾斜

- 数据倾斜：当我们在Hive上进行查询时，因为数据的分散度不够， 导致大量数据集中在一台或者几台服务器上， 导致数据的计算速度远远低于平均计算速度， 计算过程特别耗时。
- 数据倾斜的表现：任务进度长时间维持在99%，查看任务监控页面，发现只有少量子任务未完成。

## 小表Join大表

- Hive 会假定查询中最后一个表是最大的表， 在对每行记录进行连续操作时， 它会尝试将其他表缓存起来，然后扫描最后那个表进行计算。因此，我们在查询时，要保证连续查询中的表的大小从左到右依次是增加的。
- 假如，在 a, b 两个表中，b表最小， 则 写sql时需让b表在左，a表在右：

```
SELECT    a.price_close, b.price_closeFROM    b JOIN a ON b.ymd = a.ymd AND b.symbol = a.symbolWHERE    a.symbol = 'APPLE'---Hive支持使用/*+STREAMTALBE*/语法指定哪张表是大表， 不需要排序SELECT    /*+3`'LKLLGFG Streamtable(a)*/ a.price_close, b.price_closeFROM    a JOIN B on a.ymd = b.ymd AND a.symbol = b.symbolWHERE    a.symbol = 'Apple'
```

## 大表JOIN大表

1. 空key过滤 有时join超时是因为某些key对应的数据太多，而相同key对应的数据都会发送到相同的reducer上，从而导致内存不够。此时我们应该仔细分析这些异常的key，很多情况下，这些key对应的数据是异常数据，我们需要在sql语句中进行过滤。

```
Select  *From   a Join  bOn   a.user_id is not nullAnd   a.user_id = b.user_idUnion allSelect  * from  awhere  a.user_id is null
```

1. 空key转换 有时虽然某个key为空对应的数据很多，但是相应的数据不是异常数据，必须要包含在join的结果中，此时我们可以表a中key为空的字段赋一个随机值，是的数据随机均匀地分布到不同的reducer上。
- 把空值的 key 变成一个字符串加上随机数，就能把倾斜的数据分到不同的 reduce 上 ,解决数据倾斜问题。
- 需要用到Case When … Else…End语法

```
select n.*from  nullidtable n full join bigtable o oncase when n.id is null then concat('hive', rand()) else n.id end  = o.id;
```

## count(distinct) 去重统计

- 数据量大时，由于count distinct 操作需要用一个 reduce task 来完成， 这一个reduce 需要处理的数据量太大，会导致整个job很难完成，一般 count distinct 使用先group by 再 count的方式替换。

```
select count(distinct id) from bigtableselect count(id) from (select id from bigtable group by id) a
```

## 避免笛卡尔积

尽量避免产生笛卡尔积，如join时不加on条件，或无效的on条件。hive只能使用1个reducer来完成笛卡尔积

## 行列过滤

1. 列处理： 在查询中， 避免使用 select *, 使用条件限制取需要的列。
2. 行处理： 在分区剪裁中，当使用join外关联时，如果将副表的过滤条件写在where后面，那么就会先全表关联，之后再过滤, 这样会耗费资源。

```
select o.id from bigtable b join ori o on.id = b.id where o.id <=10;select b.id from bigtable bjoin (select id from ori where id <=10) o on b.id = o.id)
```

```
SELECT    a.price_close, b.price_closeFROM    b JOIN a ON b.ymd = a.ymd AND b.symbol = a.symbolWHERE    s.symbol = 'APPLE'--正确的写法是将 where 条件写在 on 后面SELECT    a.price_close, b.price_closeFROM    b JOIN a ON ( b.ymd = a.ymd AND b.symbol = a.symbol and s.symbol = 'APPLE'
```

## union all 子查询避免中使用 group by等

- union all 子查询避免中使用 group by【替换 count(distinct) 除外】、count(distinct)、max、min等。

```
with a1 as (        select            user_account,            hit_date        from            data        where            hit_date between '2018-12-01' and '2018-12-13'            and            nbtn_name like "%支付宝%"        union all         select            user_account,            hit_date        from            data        where            hit_date between '2018-12-01' and '2018-12-13'        and        nbtn_name like "%支付宝%")select    hit_date,    count(user_account) as pvfrom    a1group by    hit_date
```

## 避免不同数据类型进行关联

- 使用CAST函数对数据类型进行转换，语法为cast(value AS TYPE)

```
select 
    a.price_close,
    b.price_close
from
    a join b  on a.user_id = cast(b.user_id as string)
where
    hit_date between '2018-11-01' and '2018-11-02'
    and 
    a.symbol = 'apple'
```

Hive的查询注意事项以及优化总结： 1. 尽量尽早过滤数据，减少每个阶段的数据量。对于分区表要加分区，同时只选择需要使用到的字段

1. 对历史库的计算经验
2. 尽量原子化操作，尽量避免一个SQL包含复杂逻辑，可以使用中间表来完成复杂的逻辑
3. join操作 小表要注意放在join的左边，否则会引起磁盘和内存的大量消耗
4. 如果union all的部分个数大于2，或者每个union部分数据量大，应该拆成多个insert into语句，实际测试过程中，执行时间能提升50%

# 用python脚本连接数据库

作为一名数据分析师，日报、周报、月报数据一个也不能少。 相应的， 就要在数据库中提取大量的数据， 并处理大量的Excel表格。

在提取和处理数据的过程中， 对于一些重复性的劳动， 写个Python脚本来实现半自动化， 能够大幅提高自己的工作效率。 以下是自己工作中的一点总结经验。

1. 首先， 用Python连接数据库

对于数据库的ip地址，用户名，密码等， 如果不清楚，或数据库连接不上， 需要和开发人员对接

```
from pyhive import hive import timeconn = hive.Connection(host='ip地址', port=10000, username='用户名', database = 'default', auth='NOSASL')cursor = conn.cursor()# 获得连接的游标
```

1. 设置开始和结束时间 可以用python中的time函数设置时间

```
startdate = '2018-09-01'enddate   = '2018-09-19'
```

1. 用Python中的format函数将日期传入{}中
- python中写sql脚本时， 需要用, 。
- 日期用两个{}来代替， 用format函数将开始日期与结束日期传入

```
# 提取积分类uv,pv数据sql_jifenxinxi_an = """select    count(distinct user_account) as uv,    count(1) as pv from    computer_view.data where    hit_date between "{}" and "{}"    and    (btn_position like "服务-查询-积分信息%"    or    btn_home = "积分-扇形左"    ) limit 1000""".format(startdate,enddate)# format 插入时间cursor.execute(sql_jifenxinxi_an)# 运行此语句cursor.fetchall()#fetchall():接收全部的返回结果行.
```

我们可以按照这个格式写工作中需要运行的多个SQL语句。 这样， 当脚本运行的时候， 我们可以腾出时间来去干其他工作， 等过一段时间，所有的SQL语句都跑完了， 我们再进行统一的整理。

---

# 参考资料：

[讲讲 group 的plus版-张俊红](https://mp.weixin.qq.com/s/Xw5DOHHGh838w8YXT9lO5g)

---

---

[https://www.youtube.com/embed/f3uqAVsOxsM](https://www.youtube.com/embed/f3uqAVsOxsM)