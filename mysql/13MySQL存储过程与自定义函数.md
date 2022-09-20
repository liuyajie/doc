#### 一、预定义查询

```mysql
# 使用预定义查询，可以防止sql注入，更小的解析开销，动态的查询条件
# 因为预定义查询有自动转换的功能，不需要手工拼接
set @s = 'select * from employees where 1 = 1';
set @s = concat(@s,' and gender="m"');
set @s = concat(@s,' and birth_date>="1991-01-01"');
set @s = concat(@s,' order by emp_no limit ?,?');
set @page_no = 0;
set @page_count = 10;
prepare stmt from @s;
execute stmt using @page_no,@page_count;
deallocate prepare stmt;


# sql注入
select * from user where phone = '18338467736' and password=md5('19911121') or 1 = 1;
```

#### 二、MDL

```shell
# replace into 
# 先尝试插入，如果插入不成功，则删除再重新插入
# 如果有冲突，先删除再插入
```

#### 三、存储过程

```mysql
# 临时表
create temporary table a( a bigint); # 基于会话的临时表

# 临时表的结构放在tmp下边
# 临时表的数据放在数据目录下的ibtmpl中
show variables like '%tmp%';
```

```mysql
# 存储过程
drop table if exists tbl_proc_test;
create temporary table tbl_proc_test (num bigint);

drop procedure if exists proc_test;
delimiter //
create procedure proc_test
(in total int,out res int)
begin
	declare i int;
	set i = 1;
	set res = 1;
	if total <= 0 then
		set total = 1;
	end if;
	while i <= total do
		set res = res * i;
		insert into tbl_proc_test(num) values (res);
		set i = i+1;
	end while;
	select * from a;
end; //
delimiter ;

# 调用存储过程
call proc_test(10,@a);

# 查看当前存储过程
show procedure status like '%proc%' \G
# 查看某个存储过程的具体信息
select * from information.schema.ROUTINES where SPECIFIC_NAME= 'proc_test'

# 修改存储过程的元信息
# 如果存储过程的创建者被删除了，则存储过程不能使用
# 需要修改之后，重启mysql，这样才生效
update mysql.proc set definer = 'root@%';

# 存储过程返回结果集
drop procedure  if exists test1;
delimiter //
CREATE PROCEDURE test1()
BEGIN
select * from tbl_proc_test;
select * from tbl_proc_test;
END;//
delimiter ;

call test1();
```

```mysql
# 自定义函数 还不如存储过程
delimiter //
create function func_test1(total int)
returns int
begin
	declare i int;
	declare res int;
	set i = 1;
	set res = 1;
	if total <= 0 then
		set total = 1;
	end if;
	while i <= total do
		set res = res * i;
		insert into tbl_proc_test(num) values (res);
		set i = i+1;
	end while;
	return res;
end;//
delimiter ;

select func_test1(10);
```

```mysql
# 触发器 用来做校验数据
create table stu(
name varchar(10),
score int(11)
);

delimiter //
create trigger trg_upd_score
before update on `stu`
for each row
begin
	if NEW.score < 0 then
	set new.score = 0;
	elseif NEW.score > 100 then
	set new.score = 100;
	end if;
end; //
delimiter ;
```

```mysql
# 视图 类似于子查询，可以固化起来，供别人使用
create view v_employees as select * from employees;

select * from v_employees;
```

```mysql
# 事件 定时器，到一定时间触发
# 首先打开事件调度
show variables like '%event%';
[mysqld]
event_scheduler = 1;

# 一小时之后触发，只触发一次
create event myevent on schedule at current_timestamp + interval 1 hour do
update test.user set username = 'zz';

# 每小时触发一次
create event e_hourly on schedule every 1 hour comment '每小时执行1次清理工作' do
delete from user.log_time;

# 可以调用存储过程
create event e_hourly on schedule every 1 hour comment '每小时执行1次清理工作' do
call test1();
```

































