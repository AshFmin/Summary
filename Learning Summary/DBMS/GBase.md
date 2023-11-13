# GBase学习

### 基础SQL

```sql
-- create table


```





### merge into

```sql
-- gbase的 merge into 和 oracle 不同 有很多限制
merge into tb_A
using ()
on ()
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



