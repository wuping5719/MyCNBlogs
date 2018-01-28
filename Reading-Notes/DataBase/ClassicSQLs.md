<h2>［经典SQL语句大全］ :memo: </h2> 

* 1.复制表(只复制结构, 源表名：a, 新表名：b) (Access可用)
```sql
  法一：select * into b from a where 1<>1; (仅用于 SQlServer)
  法二：select top 0 * into b from a;
```
* 2.拷贝表(拷贝数据, 源表名：a, 目标表名：b) (Access可用)
```sql
  insert into b(a, b, c) select d,e,f from b;
```
* 3.跨数据库之间表的拷贝(具体数据使用绝对路径) (Access可用)
```sql
  insert into b(a, b, c) select d,e,f from b in ‘具体数据库’ where 条件;
  例子：... from b in '"&Server.MapPath(".")&"\data.mdb" &"' where ...
```
* 4.子查询(表名1：a, 表名2：b)
```sql
  select i,j,k from a where i IN (select d from b);
  或者: select i,j,k from a where i IN (1,2,3);
```
* 5.外连接查询(表名1：a, 表名2：b)
```sql
  select a.i, a.j, a.k, b.m, b.n, b.q from a LEFT OUT JOIN b ON a.i = b.m;
```
* 6.在线视图查询(表名1：a)
```sql
  select * from (SELECT i, j, k FROM a) t where t.i > 1;
```
