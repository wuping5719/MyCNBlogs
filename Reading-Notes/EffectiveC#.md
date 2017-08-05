<h2>《Effective C#》 :books: </h2> 

> BillWagner, Wagner  著    机械工业出版社

```C#
1.始终使用属性 (property)，而不是可直接访问的 Data Member。
  Always Use Properties Instead of Accessible Data Members.

2.为你的常量选择 readonly 而不是 const。
 Prefer readonly to const.

3.选择 is 或者 as 操作符而不是做强制类型转换。
 Prefer the is or as Operators to Casts. 

4.用条件属性而不是 # if。
 Use Conditional Attributes Instead of # if. 

5.始终提供 ToString()。
 Always Provide ToString(). 

6.区别值类型数据和引用类型数据。
  Distinguish Between Value Types and Reference Types.

7.选择恒定的原子值类型数据。
 Prefer Immutable Atomic Value Types. 

8.确保 0 对值类型数据是有效的。
 Ensure that 0 Is a Valid State for Value Types.

9.明白几个相等运算之间的关系。
 Understand the Relationships Among Reference Equals(), static Equals(), instance Equals(), and operator==.

10.明白 GetHashCode() 的缺陷。
 Understand the pitfalls of GetHashCode(). 

11.选择 foreach 循环。
 Prefer foreach Loops. 

12.选择变量初始化而不是赋值语句。
 Prefer Variable Initializers to Assignment Statements 

13.用静态构造函数初始化类的静态成员。
 Initialize Static Class Members with Static Constructors. 

14.使用构造函数链。
 Utilize Constructor Chaining. 

15.使用 using 和 try/finally 来做资源清理。
 Utilize using and try/finally for Resource Cleanup. 
 ① SqlConnection myConnection = null;
   using (myConnection == new SqlConnection(connString)) 
   { 
      myConnection.Open();
   }
 ② try 
   {
      myConnection == new SqlConnection(connString);
      myConnection.Open();
   }
   finally 
   {
      myConnection.Dispose();
   }
   
16.垃圾最小化。
  Minimize Garbage. 

17.装箱和拆箱的最小化。
  Minimize Boxing and Unboxing.
 
18.实现标准的处理(Dispose)模式。
  Implement the Standard Dispose Pattern.

19.选择定义和实现接口，而不是继承。
 Prefer Defining and Implementing Interfaces to Inheritance. 

20.明辨接口实现和虚函数重载的区别。

21.用委托来表示回调。

22.用事件定义对外接口。

23.避免返回内部类对象的引用。

24.选择声明式编程而不是命令式编程。

25.让你的类型支持序列化。

26.用 IComparable 和 IComparer 实现对象的顺序关系。

27.避免使用 ICloneable。

28.避免转换操作。

29.仅在对基类进行强制更新时才使用 new 修饰符。

30.选择与 CLS 兼容的程序集。

31.选择小而简单的函数。

32.选择小而内聚的程序集。
```
