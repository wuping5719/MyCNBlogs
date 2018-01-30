<h2>［经典SQL语句大全］ :memo: </h2> 

* 1.复制表(只复制结构, 源表名：table1, 新表名：table2) (Access可用)
```sql
  法一：select * into table2 from table1 where 1<>1; (仅用于 SQlServer)
  法二：select top 0 * into table2 from table1;
```
* 2.拷贝表(拷贝数据, 源表名：table1, 目标表名：table2) (Access可用)
```sql
  insert into table2(a, b, c) select d,e,f from table1;
```
* 3.跨数据库之间表的拷贝(具体数据使用绝对路径) (Access可用)
```sql
  insert into table1(a, b, c) select d,e,f from table1 in ‘具体数据库’ where 条件;
  例子：... from table1 in '"&Server.MapPath(".")&"\data.mdb" &"' where ...
```
* 4.子查询(表名1：table1, 表名2：table2)
```sql
  select i,j,k from table1 where i IN (select d from table2);
  或者: select i,j,k from table1 where i IN (1,2,3);
```
* 5.外连接查询(表名1：a, 表名2：b)
```sql
  select a.i, a.j, a.k, b.m, b.n, b.q from a LEFT OUT JOIN b ON a.i = b.m;
```
* 6.在线视图查询(表名1：table1)
```sql
  select * from (SELECT i, j, k FROM table1) t where t.i > 1;
```
* 7.between 的用法, between 限制查询数据范围时包括了边界值, not between 不包括.
```sql
  select * from table1 where time between time1 and time2;
  select a, b, c, from table1 where a not between 数值1 and 数值2;
```
* 8.in 的使用方法.
```sql
  select * from table1 where a [not] in (‘值1’, ’值2’, ’值4’, ’值6’);
```
* 9.两张关联表，删除主表中已经在副表中没有的信息.
```sql
   delete from table1 where not exists ( select * from table2 where table1.field1 = table2.field1 );
```
* 10.四表联查.
```sql
  select * from table1 left inner join table2 on table1.a = table2.b 
  right inner join table3 on table1.a = table3.c 
  inner join table4 on table1.a = table4.d where ...
```
* 11.日程安排提前五分钟提醒.
```sql
   select * from 日程安排 where datediff('minute', f开始时间, getdate()) > 5;
```
* 12.一条 sql 语句搞定数据库分页.
```sql
   select top 10 b.* from (select top 20 主键字段, 排序字段 from 表名 order by 排序字段 desc) a, 表名b
   where b.主键字段 = a.主键字段 order by a.排序字段;

   关于数据库分页：
   declare @start int, @end int
   @sql nvarchar(600)
   set @sql = ’select top ’ + str(@end - @start + 1) + ’ + from T where rid not in(
   select top ’ + str(@str - 1) + ’ Rid from T where Rid > -1)’
   exec sp_executesql @sql
   注意：在 top 后不能直接跟一个变量，所以在实际应用中只有这样的进行特殊的处理。
   Rid 为一个标识列，如果 top 后还有具体的字段，这样做是非常有好处的。因为这样可以避免 top 的字段如果是逻辑索引，
 查询结果后实际表中的不一致(逻辑索引中的数据有可能和数据表中的不一致，而查询时如果处在索引则首先查询索引).
 ```
* 13.前 10 条记录.
```sql
   select top 10 * form table1 where 范围;
```
* 14.选择在每一组 b 值相同的数据中对应的 a 最大的记录的所有信息(类似这样的用法可以用于论坛每月排行榜, 
每月热销产品分析, 按科目成绩排名, 等等).
```sql
   select a, b, c from tablename ta where a = (select max(a) from tablename tb where tb.b = ta.b);
```
* 15.包括所有在 TableA 中但不在 TableB 和 TableC 中的行并消除所有重复行而派生出一个结果表.
```sql
   (select a from tableA ) except (select a from tableB) except (select a from tableC);
```
* 16.随机取出 10 条数据.
```sql
   select top 10 * from tablename order by newid();
```
* 17.随机选择记录.
```sql
   select newid();
```
* 18.删除重复记录.
```sql
  (1) delete from tablename where id not in (select max(id) from tablename group by col1, col2, ...);
  (2) select distinct * into temp from tablename;
      delete from tablename;
      insert into tablename select * from temp;
   评价： 这种操作牵连大量数据的移动，这种做法不适合大容量的数据操作.
  (3) 例如：在一个外部表中导入数据，由于某些原因第一次只导入了一部分，但很难判断具体位置，这样只有在下一次全部导入，
这样也就产生好多重复的字段，怎样删除重复字段?
      alter table tablename;
      -- 添加一个自增列
      add column_b int identity(1, 1);
      delete from tablename where column_b not in(
         select max(column_b) from tablename group by column1, column2, ...);
      alter table tablename drop column column_b;
```
* 19.列出数据库里所有的表名.
```sql
   select name from sysobjects where type = 'U';  // U 代表用户
```
* 20.列出表里的所有的列名.
```sql
   select name from syscolumns where id = object_id('TableName');
```
* 21.列示 type、vender、pcs 字段，以 type 字段排列，case 可以方便地实现多重选择，
  类似 select 中的 case。
```sql
  select type, sum(case vender when 'A' then pcs else 0 end), sum(case vender when 'C' then pcs else 0 end), 
sum(case vender when 'B' then pcs else 0 end) FROM tablename group by type;
```
* 22.初始化表 table1。
```sql
   truncate table table1;
```
* 23.选择从 10 到 15 的记录。
```sql
   select top 5 * from (select top 15 * from table order by id asc) table_别名 order by id desc;
```
* 24.SQL SERVER 中直接循环写入数据.
```sql
   declare @i int
   set @i=1
   while @i<30
   begin
     insert into test (userid) values(@i)
     set @i=@i+1
   end
```
* 25.查看与某一个表相关的视图、存储过程、函数.
```sql
   select a.* from sysobjects a, syscomments b where a.id = b.id and b.text like '%表名%';
```
* 26.查看当前数据库中所有存储过程.
```sql
   select name as 存储过程名称 from sysobjects where xtype = 'P';
```
