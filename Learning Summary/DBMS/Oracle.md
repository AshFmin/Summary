## 复制数据库表

```sql
-- 复制表结构
create table tb_new as 
select * 
from tb_old
where 1 = 2;

-- 复制表结构及数据
create table tb_new as 
select * 
from tb_old;
```



## Merge into

```sql
merge into table_name  alias1   --目标表 可以用别名
using (table|view|sub_query) alias2      --数据源表 可以是表、视图、子查询
on (join condition)   --关联条件
when matched then              --当关联条件成立时 更新，删除，插入的where部分为可选 
  --更新
  update  table_name set  col1=colvalue  where……
  --删除  
  delete from table_name  where  col2=colvalue  where……
--可以只更新不删除 也可以只删除不更新。
--如果更新和删除同时存在，删除的条件一定要在更新的条件内，否则数据不能删除。
when not matched then      --当关联条件不成立时   
  --插入
  insert (col3) values (col3values)  where…… 
 when not matched  by source then      --当源表不存在，目标表存在的数据删除
  delete; 
```

