#### 一、ORDER BY

```shell
select * from lineitem order by l_discount limit 10000;
# 排序比较慢的话，使用这个排序内存
show variables like '%sort_buffer_size%';

# 查询当前会话的状态值
show status like '%sort%';
show global status like '%sort%';

# 刷新 状态值
flush status;

[mysqld]
# session memery
sort_buffer_size=256M
```

#### 二、GROUP BY

```shell
# 使用group by会使用到临时表
select date_format(o_orderDATE,'%Y-%m'),o_clerk,count(1),sum(o_totalprice),avg(o_totalprice) from orders group by date_format(o_orderdate,'%Y-%m'),o_clerk;

# 如果聚合比较慢的话，可以查看临时表使用次数，如果太大的话，可以修改临时表的大小
show status like '%tmp%';
[mysqld]
# session memery
tmp_table_size=256M

# count(1) === count(*) count(a)与前两者不一样
# count(a) 如果a有null值的话，则不会计算这一行

# having 过滤，对聚合之后的表达式进行过滤，只能使用聚合的字段，使用其他字段会报错
# where 先过滤，后聚合
select date_format(o_orderDATE,'%Y-%m') month,count(*) count from orders group by date_format(o_orderdate,'%Y-%m') having count > 1 and month >= '2010-01' and month <= '2011-01';

# 使用聚合之后，字段不能出现不是聚合的字段
# 可以修改是否使用严格模式
# 根据业务是否修改，一开始最好就使用严格模式
[mysqld]
sql_mode='ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'


# 聚合函数使用
select date_format(o_orderDATE,'%Y-%m') month,count(*) count,group_concat(price order by price separator '-') 
from orders group by date_format(o_orderdate,'%Y-%m') having count > 1 and month >= '2010-01' and month <= '2011-01';
```

#### 三、子查询

```mysql
# 独立子查询
select * from a where a.x in(1,2,3);
select * from a where a.x in(select y from b);
# 相关子查询，使用了外部的列的查询，并与外部表发生了关联
select * from a where exists(select * from b where a.x = b.y)

# in是独立子查询，速度比较快
select * from orders where o_orderdate in (select max(o_orderdate) from orders group by date_format(o_orderdate,'%Y%m'));
# exists是相关子查询，速度比较慢
select * from orders a where exists(select max(o_orderdate) from orders b group by date_format(o_orderdate,'%Y%m') having date_format(o_orderdate,'%Y%m') = a.o_orderdate);

# in的问题
# not in 如果有null的话，只会返回 0 或 null
# 一定要过滤掉null，否则只能为空
select 'a' not in('a','b');# 返回0
select 'c' not in ('a','b',null);# 返回null

# not exists 与 not in的区别
select * from a where userid not in(select y from b where y is not null);
select * from a where not exists(select y from b where y = a.userid);
```

#### 四、分页

```mysql
# 翻页的时候，limit 越大越慢
# 只能上一页或下一页那种
# 获取上一页的最后一个ID
select * from employees where (birth_date,emp_no) > ('2022-03-16','19911121') order by birth_date,emp_no limit 30;

# 多查询出来几页，尽量减少翻页

```

#### 五、连接

```mysql
# 两种关联写法，没有任何区别
# 不同的标准写法
select a,b from x,y where x.a = y.a;
select * from a join b on a.id = b.id;


select emp_no,title from titles where (emp_no,to_date) in (
	select emp_no,max(to_date) from titles group by emp_no
);


# where on 的区别
# on 做表之间的过滤条件
# where 是对关联结果做的过滤 


# 写关联sql语句时，注意条件是否互斥
# 可以先写派生表
select c.c_name,c.c_phone,c.c_address from customer c left join (
	select * from orders where o_orderdate >= '1997-01-01' and o_orderdate < '1998-01-01'
) o on c.custkey = o.custkey left join
nation n on c.c_nationkey = n.n_nationkey where o.orderkey is null and n.n_name = 'CANADA';


# 行号问题，添加一个行号
set @a:=0;
select @a:=@a+1 as rownum from user limit 10;
# 使用关联形式来搞，这样就是笛卡尔积正好等于它自己
select @a:=@a+1,username from user,(select @a:=0) limit 10;


# 相关子查询 开销非常大，因为每一条都要去计算
select emp_no,(
	select count(*) from employees t2 where t2.emp_no <= t1.emp_no
) row_num from employees t1
order by emp_no limit 10;
```

























