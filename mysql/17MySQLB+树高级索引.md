#### 一、作业题

```mysql
# 查询某张表在哪个数据库当中
use information_schema;
select table_schema,table_name from information_schema.tables where table_name = 'schema_unused_indexes';

# 查找哪个表没有主键索引
use information_schema;
select table_schema,table_name from tables where (table_schema,table_name) not in(
    select table_schema,table_name from statistics where index_name = 'PRIMARY' group by table_schema,table_name
) and table_schema not in('information_schema','sys','mysql','performance_schema');

# 创建了索引，却从来没有被使用，就算使用了也得找见一条数据才算
use sys;
select * from schema_unused_indexes;

```

#### 二、索引

```mysql
# 作用1 用来快速定位

# 作用2 从排序的数据里边取数据
show variables like 'sort_buffer_size';# 如果排序时太慢，有可能排序缓存太小

# 这样搞比较慢，但是如果给排序加了索引，那么会很快
explain select * from orders order by o_totalprice desc limit 10;
alter table orders add index inx_o_totalprice(o_totalprice);
```

#### 三、什么时候添加索引

```mysql
# 高选择性、高离散性创建索引比较快
# 对姓名加索引比对性别要好

# 查看表的高选择性
use information_schema;
select s.table_schema,s.table_name,s.column_name,s.index_name,s.CARDINALITY,TABLE_ROWS,concat(round(s.CARDINALITY/table_rows*100),'%') from statistics s join tables t on s.table_schema = t.table_schema and s.table_name = t.table_name where table_rows>0 and s.CARDINALITY > 0 and s.index_name !='PRIMARY';


# 复合索引(a,b) 
# a 有序
# (a,b) 有序
# b 无序
select * from t where a = ?;# a 有序
select * from t where a = ? and b = ?;# (a,b) 有序
# 它没有使用索引
select * from t where b = ?;# b 无序
# 它使用了复合索引，要创建复合索引，不能只创建a的索引
select * from t where a = ? order by b;# (a,b) 有序
# 快速查找排序次数最多的sql
use sys;
select * from sys.statement_with_sorting limit 2;


# 索引覆盖，不需要回表也能找见所需的字段
(a,b,c)
select a,b,c from t where a >'1' and a < '3';

# 这种情况也算索引覆盖，不需要回表也能找到所需的数据
# 但是它的效率不一定最高，就像下边这种情况
(userid,buy_date)
select count(*) from buy_log where buy_date>='2011-01-01' and buy_date <'2011-02-01';

# 查找冗余索引，创建了复合索引之后，又创建了该列的索引
use sys;
select * from schema_redundant_indexes;


# 例子1 会使用filesort
# 添加它们3个的复合索引就不会文件排序了
explain select * from orders where o_custkey=1 order by o_orderDATE,o_orderstatus;
alter table orders add index idx_a_b_c(o_custkey,o_orderdate,o_orderstatus);

# 例子2 仍会使用filesort
explain select * from orders where o_custkey=1 order by o_orderDATE desc,o_orderstatus;

# 例子3, 不会使用filesort
explain select * from orders where o_custkey=1 order by o_orderDATE desc,o_orderstatus desc;

# 例子4 可以创建索引时，指定排序方向 mysql8.0可以
alter table orders add index idx_a_b_c(o_custkey,o_orderdate desc,o_orderstatus);

# 例子5 创建函数索引，解决5.7的useing filesort
# 创建虚拟列
alter table orders add column as function();
```

#### 四、SQL JOIN 算法

```mysql
				简单扫表    索引扫表     缓存扫表
驱动扫表次数       1           1            1
外表扫表次数       3           3            1
扫描成本		 3*4          3            3
比较次数	      12          12           12

索引扫表时，两个列都加上索引

缓存扫表时，调整该参数，没有索引时，尽量放大该参数
show variables like '%join_buffer_size%';


```

#### 五、回表

````mysql
# 回表，查询二级索引时，会去查询主数据，而且是随机IO速度比较慢
# orders表总共有150w条数据，以下sql可以查出来50w条数据
# 光回表就是50w次，那么IO就是50W*3
# 如果全表扫描的话，可能只有1w次，所以还不如不用索引
# 数据量超级多，不会使用索引，直接使用全表扫描
select * from orders where o_orderdate >= '1991-01-01';

# 数据量比较多，使用了索引且有回表，这个时候使用下边的方式解决，把随机回表改成顺序回表，8.0已经解决这个问题了
select * from orders where o_orderdate >= '1998-01-01';

# 二级索引（key，primarykey） ，因为每次回表时，是随机回表，可以改成顺序回表，但仍然需要很多次IO
select /*+ mrr(orders) */ * from orders where o_orderdate >= '1991-01-01';
````

#### 六、explain执行计划

```mysql

```

























