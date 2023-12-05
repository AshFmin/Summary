# GBase学习

### 基础SQL

```sql
-- create table 

-- distributed by hash列

-- 查询表的hash_col
select * from gbase.table_distribution where dbname='' and tbname='';


-- 查看数据加载错误原因 
show gcluster load logs task_id limit 1,10;

-- 查看表结构
show columns from schema.tablename;
eg: show columns from gbaseJTX.test_columns;

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



### gccli 和 gncli

```shell
# gccli 对应/opt/gbase/gcluster/server/bin/gbase
# gncli 对应/opt/gbase/gnode/server/bin/gbase

# gccli连接的是整个集群 其SQL也会分发到所有节点执行
# gncli只连接集群某个节点
# 对应的普通分布表 在gccli里看到的是简单的表名 例如t1,
# 在gncli里则包含了节点分片的名字 例如t1_n3。

# 部分说法gccli是gbase的软连接 这种说法并不正确
# gccli源码
# 可以看到 gccli并不是gbase的软连接 而是在gbase的基础上进行了环境变量的配置 类似装饰器模式





```



### 字符串拼接

```sql
select tb.A || tb.B || '123' from tb_test tb;
-- Gbase 做字符串拼接时 若某一项为 NULL 那么整个结果就会为 NULL


```

