<h2>《Effective C++》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```html
1.视 C++ 为一个语言联邦。
   C：C++ 以 C 为基础。区块(blocks)、语句(statements)、预处理器(preprocessor)、
内置数据类型(built-in data types)、数组(arrays)、指针(pointers)等统统来自 C。
   Object-Oriented C++: classes(包括构造函数和析构函数)、封装(encapsulation)、继承(inheritance)、
多态(polymorphism)、virtual 函数(动态绑定)等等。
   Template C++：C++ 的泛型编程(generic programming)部分。
   STL：template 程序库，它对容器(containers)、迭代器(iterators)、算法(algorithms)以及
函数对象(function objects)的规约有极佳的紧密配合与协调。

2.尽量以 const, enum, inline 替换 #define。(宁可以编译器替换预处理器)
  定义常量指针：const char* const authorName = "Nick";
               const std::string authorName("Nick");
  class GamePlayer {
     private:
        enum { NumTurns = 5; }   // “the enum hack”——令 NumTurns 成为 5 的一个记号名称。
        int scores[NumTurns];
        ...
  }
  (1) 对于单纯常量，最好以 const 对象或 enum 替换 #define。
  (2) 对于形似函数的宏(macros)，最好改用 inline 函数替换 #define。
   用：template<typename T>
       inline void callWithMax(const T& a, const T& b) {
          f(a > b ? a : b);
       }
   替换：#define CALL_WITH_MAX(a, b) f((a) > (b) ? (a) : (b))

3.尽可能使用 const。
```
