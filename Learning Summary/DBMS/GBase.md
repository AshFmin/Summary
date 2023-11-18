# GBase学习

### 基础SQL

```sql
-- create table 

-- distributed by hash列

-- 查询表的hash_col
select * from gbase.table_distribution where dbname='' and tbname='';


-- 查看数据加载错误原因 
show gcluster load logs task_id limit 1,10;


```





### merge into

```sql
-- gbase的 merge into 和 oracle 不同 有很多限制
merge into tb_A
using ()
on ()
-- on条件必须包含 hash_col


-- update 和 insert 不能包含where子句



```



### GBase 临时表

```sql
create temporary table tb(
	xxx
);

-- gbase的临时表与oracle不同 
-- oracle中可以创建 COMMIT PRESERVE ROWS 会话临时表 和  ON COMMIT DELETE ROWS 事务临时表
-- gbase中只支持 会话临时表 
-- 即不同连接访问的表不会产生冲突 当连接断开时 会 truncate table
```



