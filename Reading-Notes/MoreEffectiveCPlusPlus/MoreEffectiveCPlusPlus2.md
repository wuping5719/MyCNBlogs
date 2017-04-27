<h2>《More Effective C++》 :books: </h2> 

> [美] Scott Meyers 著    电子工业出版社

```c++
11.禁止异常 (exception) 流出 destructors 之外。
   (1) 可以避免 terminate 函数在 exception 传播过程的栈展开 (stack-unwinding) 机制中被调用。
   (2) 可以协助确保 destructors 完成其应该完成的所有事情。
    Session::~Session() {
       try {
          logDestruction(this);
       } catch (...) { }
    }

12.了解 “抛出一个 exception” 与 “传递一个参数” 或 “调用一个虚函数” 之间的差异。
  “传递对象到函数去，或是以对象调用虚函数” 和 “将对象抛出成为一个 exception” 之间，有 3 个主要差异：
   (1) exception objects 总是会被复制，如果以 by value 方式捕捉，它们甚至被复制两次。至于传递给函数参数的
对象则不一定得复制。
   (2) “被抛出成为 exceptions” 的对象，其被允许的类型转换动作，比 “被传递到函数去” 的对象少。
   (3) catch 子句以其 “出现于源代码的顺序” 被编译器检验比对，其中第一个匹配成功者便执行；而当我们以某对象调用
一个虚函数，被选中执行的是那个 “与对象类型最佳吻合” 的函数，不论它是不是源代码所列的第一个。
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_exceptions.png" />

```c++
13.以 by reference 方式捕捉 exceptions。
   void doSomething() {
      try {
         someFunction();
      } catch (exception& ex) {
         cerr << ex.what();     // 调用 Validation_error::what() 而非 exception::what()
         ...
      }
   }

14.明智运用 exception specifications。
   所有非预期的 exceptions 都以 UnexpectedException objects 取而代之。
   class UnexpectedException {};     
   void convertUnexpected() {   // 如果有一个非预期的 exception 被抛出，便调用此函数
       throw UnexpectedException();
   }
   set_unexpected(convertUnexpected);  // convertUnexpected 取代默认的 unexpected 函数

15.了解异常处理 (exception handling) 的成本。
   使用 try 语句块，代码大约整体膨胀 5% ~ 10%，执行速度亦大约下降这个数。
   
16.谨记 80 - 20 法则。
   80 - 20 法则：一个程序 80% 的资源用于 20% 的代码身上。

17.考虑使用 lazy evaluation (缓式评估)。
   lazy evaluation 在许多领域中都可能有用途：可避免非必要的对象复制，可区别 operator[] 的读取和写动作，
可避免非必要的数据库读取动作，可避免非必要的数值计算动作。

18.分期摊还预期的计算成本。
   可以通过超急评估 (over-eager evaluation) 如缓存 (caching) 和 预先取出 (prefetching) 
等做法分期摊还预期运算成本。
    int findCubicleNumber(const string& employeeName) {
        // 定义一个 static map 以持有 (employee name, cubicle number) 数据对。这个 map 用来做局部缓存
        typedef map<string, int> CubicleMap;
        static CubicleMap cubes;
        // 尝试在 cache 中针对 employeeName 找出一笔记录。如果确有一笔， 迭代器便会指向该笔找到的记录
        CubicleMap::iterator it = cubes.find(employeeName);
        // 如果找不到任何吻合记录，迭代器的值将会是 cubes.end()。如果这样，则查询数据库，然后把它加进 cache 中
        if (it == cubes.end()) {
           int cubicle = the result of looking up employeeName`s cubicle number int the database;
           cubes[employeeName] = cubicle;
           return cubicle;
        } else {
           // 迭代器指向正确的 cache 记录，只需获取 (employee name, cubicle number) pair 的第二项数据
           return (*it).second;
        }
    }

19.了解临时对象的来源。
   任何时候只要你看到一个 reference-to-const 参数，就极可能会有一个临时对象被产生出来绑定至该参数上。
任何时候只要你看到函数返回一个对象，就会产生临时对象(并于稍后销毁)。

20.协助完成 “返回值优化(RVO)”。
   // 函数返回一个对象：最有效率的做法
   inline const Rational operator*(const Rational& lhs, const Rational& rhs) {
      return Rational(lhs.numerator() * rhs.numerator(), lhs.denominator() * rhs.denominator());
   }
```
