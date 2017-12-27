<h2> ［Oracle存储过程］ :memo: </h2> 

> 1.存储过程创建语法
```SQL
Create or Replace Procedure 存储过程名（param1 in type，param2 out type） 

As 
   变量1 类型（值范围）;
   变量2 类型（值范围）;

Begin

    Select count(*) into 变量1 from 表A where 列名 = param1;

    If (判断条件) then

       Select 列名 into 变量2 from 表A where 列名 = param1;

       Dbms_output.Put_line(‘打印信息’);

    Elsif (判断条件) then

       Dbms_output.Put_line(‘打印信息’);

    Else

       Raise 异常名（NO_DATA_FOUND）;

    End if;

Exception

    When others then

       Rollback;

End;

---------------------------------------------------------------------------------
注意事项：

 (1) 存储过程参数不带取值范围，in 表示传入，out 表示输出。

 (2) 变量带取值范围，后面接分号。

 (3) 在判断语句前最好先用 count（*）函数判断是否存在该条操作记录。

 (4) 用 select ... into ... 给变量赋值。

 (5) 在代码中抛异常用 raise + 异常名。
---------------------------------------------------------------------------------
```

> 2.已命名的异常
```SQL
    命名的系统异常                             产生原因 

   ACCESS_INTO_NULL                            未定义对象. 

   CASE_NOT_FOUND                     CASE 中若未包含相应的 WHEN ，并且没有设置 ELSE 时. 

   COLLECTION_IS_NULL                        集合元素未初始化. 

   CURSER_ALREADY_OPEN                        游标已经打开. 

   DUP_VAL_ON_INDEX                   唯一索引对应的列上有重复的值. 

   INVALID_CURSOR                         在不合法的游标上进行操作. 

   INVALID_NUMBER                    内嵌的 SQL 语句不能将字符转换为数字. 

   NO_DATA_FOUND                     使用 select into 未返回行，或应用索引表未初始化的.  

   TOO_MANY_ROWS                      执行 select into 时，结果集超过一行. 

   ZERO_DIVIDE                                 除数为 0 .

   SUBSCRIPT_BEYOND_COUNT             元素下标超过嵌套表或 VARRAY 的最大值. 

   SUBSCRIPT_OUTSIDE_LIMIT            使用嵌套表或 VARRAY 时，将下标指定为负数. 

   VALUE_ERROR                            赋值时，变量长度不足以容纳实际数据. 

   LOGIN_DENIED                    PL/SQL 应用程序连接到 oracle 数据库时，提供了不正确的用户名或密码.

   NOT_LOGGED_ON                          PL/SQL 应用程序在没有连接 oralce 数据库的情况下访问数据. 

   PROGRAM_ERROR                     PL/SQL 内部问题，可能需要重装数据字典 ＆ pl./SQL 系统包. 

   ROWTYPE_MISMATCH                 宿主游标变量与 PL/SQL 游标变量的返回类型不兼容. 

   SELF_IS_NULL                         使用对象类型时，在 null 对象上调用对象方法. 

   STORAGE_ERROR                          运行 PL/SQL 时，超出内存空间. 

   SYS_INVALID_ID                            无效的 ROWID 字符串.

   TIMEOUT_ON_RESOURCE                      Oracle 在等待资源时超时. 
------------------------------------------------------------------------------------
```

> 3.Oracle 存储过程基本语法
```SQL
 (1) 基本结构
  CREATE OR REPLACE PROCEDURE 存储过程名字
  (
     参数1 IN NUMBER,
     参数2 IN NUMBER
  ) IS
  变量1 INTEGER :=0;
  变量2 DATE;
  BEGIN

  END 存储过程名字

 ---------------------------------------------------------------------
 (2) SELECT INTO STATEMENT
  将 select 查询的结果存入到变量中，可以同时将多个列存储多个变量中，
  必须有一条记录，否则抛出异常(如果没有记录抛出 NO_DATA_FOUND).
  例子： 
  BEGIN
    SELECT col1,col2 INTO 变量1,变量2 FROM typestruct WHERE xxx;
    EXCEPTION
    WHEN NO_DATA_FOUND THEN
      xxxx;
  END;
  ...

 ---------------------------------------------------------------------
 (3) IF 判断
  IF v_test = 1 THEN
    BEGIN 
       do something
    END;
  END IF;

 ---------------------------------------------------------------------
 (4) WHILE 循环
  WHILE v_test = 1 LOOP
    BEGIN
       XXXX
    END;
  END LOOP;

 ---------------------------------------------------------------------
 (5) 变量赋值
  v_test := 123;

 ---------------------------------------------------------------------
 (6) 用 FOR IN 使用 CURSOR
  ...
  IS
  CURSOR cur IS SELECT * FROM xxx;
  BEGIN
     FOR cur_result IN cur LOOP
        BEGIN
            v_sum := cur_result.列名1 + cur_result.列名2
        END;
     END LOOP;
  END;
  
 ---------------------------------------------------------------------
 (7) 带参数的 CURSOR
  CURSOR C_USER(C_ID NUMBER) IS SELECT NAME FROM USER WHERE TYPEID = C_ID;
  OPEN C_USER(变量值);
  LOOP
     FETCH C_USER INTO V_NAME;
     EXIT FETCH C_USER%NOTFOUND;
     do something
  END LOOP;
  CLOSE C_USER;
 
 ---------------------------------------------------------------------
 (8) 用 PL/SQL developer debug
  连接数据库后建立一个 Test WINDOW.
  在窗口输入调用 SP 的代码, F9 开始 debug, CTRL + N 单步调试.
------------------------------------------------------------------------------------
```

> 4.Oracle存储过程的常见问题总结
```SQL
 (1) 在 Oracle 中，数据表别名不能加 as，如：
  select a.appname from appinfo a;    -- 正确
  select a.appname from appinfo as a; -- 错误
  也许，是怕和 Oracle 中的存储过程中的关键字as冲突.
 
 ---------------------------------------------------------------------
 (2) 在存储过程中，select 某一字段时，后面必须紧跟 into，如果 select 整个记录，利用游标的话就另当别论了。
  select af.keynode into kn from APPFOUNDATION af where af.appid=aid and af.foundationid=fid; -- 有into，正确编译
  select af.keynode from APPFOUNDATION af where af.appid=aid and af.foundationid=fid;  -- 没有into，编译报错.
  -- 提示：Compilation Error: PLS-00428: an INTO clause is expected in this SELECT statement.
 
 ---------------------------------------------------------------------
 (3) 在利用 select ... into ... 语法时，必须先确保数据库中有该条记录，否则会报出 "no data found" 异常。
  可以在该语法之前，先利用 select count(*) from 查看数据库中是否存在该记录，如果存在，再利用 select ... into ...
 
 ---------------------------------------------------------------------
 (4) 在存储过程中，别名不能和字段名称相同，否则虽然编译可以通过，但在运行阶段会报错。
  select keynode into kn from APPFOUNDATION where appid=aid and foundationid=fid; -- 正确运行
  select af.keynode into kn from APPFOUNDATION af where af.appid=appid and af.foundationid=foundationid;
  -- 运行阶段报错，提示 ORA-01422:exact fetch returns more than requested number of rows.
 
 ---------------------------------------------------------------------
 (5) 在存储过程中，关于出现 null 的问题。
  假设有一个表A，定义如下：
  create table A(
     id varchar2(50) primary key not null,
     vcount number(8) not null,
     bid varchar2(50) not null -- 外键 
  );
  如果在存储过程中，使用如下语句：
  select sum(vcount) into fcount from A where bid='xxxxxx';
  如果A表中不存在 bid="xxxxxx" 的记录，则 fcount = null (即使 fcount 定义时设置了默认值，
  如：fcount number(8) := 0 依然无效，fcount 还是会变成 null)，这样以后使用 fcount 时就可能有问题，
  所以在这里最好先判断一下：
  if fcount is null then
     fcount:=0;
  end if;
  这样就一切ok了。
  
 ---------------------------------------------------------------------
 (6) Hibernate 调用 Oracle 存储过程。
  this.pnumberManager.getHibernateTemplate().execute(
    new HibernateCallback() {
      public Object doInHibernate(Session session) throws HibernateException, SQLException {
        CallableStatement cs = session.connection().prepareCall("{call modifyapppnumber_remain(?)}");
        cs.setString(1, foundationid);
        cs.execute();
        return null;
      }
    });
```
