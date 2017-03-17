<h2>《Effective C++》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
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
  (1) 关键字 const 出现在星号左边，表示被指物是常量；出现在星号右边，表示指针自身是常量；
出现在星号两边，表示被指物和指针两者都是常量。
      void f1(const Widget* pw);   // f1 获得一个指针，指向一个常量的 Widget 对象
      void f2(Widget const * pw);  // f2 也是
  (2) STL 迭代器系以指针为根据塑模出来，迭代器的作用就像个 T* 指针。
      std::vector<int> vec;
      ...
      const std::vector<int>::iterator iter = vec.begin();  // iter 的作用像个 T* const
      *iter = 10;              // 没问题，改变 iter 所指物
      ++iter;                  // 错误! iter 是 const  
      std::vector<int>::const_iterator cIter = vec.begin();  // cIter 的作用像个 const T* 
      *cIter = 10;             // 错误! *cIter 是 const
      ++cIter;                 // 没问题，改变 cIter
  (3) 令函数返回一个常量值，往往可以降低客户错误而造成的意外。
      class Rational { ... };
      const Rational operator* (const Rational& lhs, const Rational& rhs);
  (4) const 成员函数。
      ① class TextBlock {
         public:
           ...
           // 返回 const 对象
           const char& operator[](std::size_t position) const { return text[position]; } 
           // 返回 non-const 对象
           char& operator[](std::size_t position) { return text[position]; } 
         private:
           std::string text;
        };
        TextBlock tb("Hello");
        const TextBlock ctb("World");
        void print(const TextBlock& ctb) {
           std::count << ctb[0];   // 调用 const TextBlock::operator[]
           ...
        }
        std::count << tb[0];      // 没问题——读一个 non-const TextBlock
        tb[0] = 'x';              // 没问题——写一个 non-const TextBlock
        std::count << ctb[0];     // 没问题——读一个 const TextBlock
        ctb[0] = 'x';             // 错误!——写一个 const TextBlock
      ② 成员函数是 const 意味着什么?
      两个流行概念：bitwise constness(又称 physical constness)和 logical constness。
      利用 mutable(可变的)释放掉 non-static 成员变量的 bitwise constness 约束：
        class CTextBlock {
          public:
            ...
            std::size_t length() const;
          private:
            char* pText;
            mutable std::size_t textLength;  // 这些成员变量可能总是会被更改，即使在 const成员函数内
            mutable bool lengthIsValid;
        };
        std::size_t CTextBlock::length() const {
           if (!lengthIsValid) {
              textLength = std::strlen(pText);   // 现在，可以改变了
              lengthIsValid = true;
           }
           return textLength;
        }
   (5) 在 const 和 non-const 成员函数中避免重复。
     当 const 和 non-const 成员函数有着实质等价的实现时，令 non-const 版本调用 const 版本可避免代码重复。
     令 non-const operator[] 调用其 const 兄弟是一个避免代码重复的安全做法
       ——即使过程中需要一个转型(casting)动作。
     class TextBlock {
       public:
         ...
         const char& operator[](std::size_t position) const {
            ... // 边界检验(bounds checking)
            ... // 志记数据访问(log access data)
            ... // 检验数据完整性(verify data integrity)
            return text[position];
         }
         char& operator[](std::size_t position) {  
            return const_cast<char&>(static_cast<const TextBlock&>(*this)[position]);
         }
         ...
     };
     这里共有两次转型：第一次用来为 *this 添加 const，第二次则是从 const operator[] 的返回值中移除 const。
```
