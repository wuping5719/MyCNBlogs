<h2>［经典SQL语句大全］ :memo: </h2> 

* 1.复制表(只复制结构, 源表名：table1, 新表名：table2) (Access可用)
```sql
  法一：select * into table2 from table1 where 1<>1; (仅用于 SQlServer)
  法二：select top 0 * into table2 from table1;
```
* 2.拷贝表(拷贝数据, 源表名：a, 目标表名：b) (Access可用)
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
