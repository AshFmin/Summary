### Oracle 创建数据表

```sql
-- oracle不能像mysql一样建表时直接将描述加在后面
-- 要在表已经建好的情况下去加
create table tb_test (
	f1 varchar2(20),
    f2 varchar2(20)
);
comment on table tb_test is 'xxx';
comment on column tb_test.xxx  is 'xxx';

-- 添加约束的时
-- default 和 not null的顺序也有要求 default在前 not null在后
create table tb_test (
	f1 varchar2(20) default 'xxx' not null,
    f2 varchar2(20)
);
```

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

## SQL LOADER

```sql
-- 使用sqlldr导入数据文件时 
-- 通常拆分 数据文件(.dat) 和 控制文件(.ctl)
-- 在控制文件中需要生成对应的数据表结构
-- 若数据表字段没有设置 则默认为char(255) 与数据库实际表字段类型无关
-- 若导入数据超出该字段 则会报错 故需要修改 .ctl文件 
-- 需要对数据表字段给出具体类型设置
```

