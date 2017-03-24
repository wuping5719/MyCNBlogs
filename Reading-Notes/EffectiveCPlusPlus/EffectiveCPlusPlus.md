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
     
4.确定对象被使用前已经先被初始化。
   (1) 为内置对象进行手工初始化，因为 C++ 不保证初始化它们。
   (2) 构造函数最好使用成员初始化列表(member initialization list)，而不要在构造函数本体内使用赋值操作(assignment)。
成员初始化列表列出的成员变量其排列次序应该和它们在 class 中的声明次序相同。
      ABEntry::ABEntry(const std::string& name, const std::string& address, 
                     const std::list<PhoneNumber>& phones)
              :theName(name), theAddress(address), thePhones(phones), numTimesConsulted(0)  // 初始化
      { }  // 构造函数本体不必有任何动作
   (3) 为免除“跨编译单元之初始化次序”问题，请以 local static 对象替换 non-local static 对象。
      class FileSystem { ... }; 
      FileSystem& tfs() {        // 这个函数用来替换 tfs 对象；它在 FileSystem class 中可能是个 static。
         static FileSystem fs;   // 定义并初始化一个 local static 对象，返回一个 reference 指向上述对象。
         return fs;
      }
      class Directory { ... };
      Directory::Directory(params) {
         ...
         std::size_t disks = tfs().numDisks();    // 原本的 reference to tfs 改为 tfs()
      }
      Directory& tempDir() {    // 用来替换 tempDir 对象
         static Directory td;   // 它在 Directory class 中可能是个 static。定义并初始化 local static 对象
         return td;             // 返回一个 reference 指向上述对象
      }

5.了解 C++ 默默编写并调用哪些函数。
   编译器可以暗自为 class 创建默认(default)构造函数、复制(copy)构造函数、copy assignment 操作符，以及析构函数。
     class Empty { };
   相当于：
     class Empty { 
       public:
           Empty() { ... }                   // 默认(default)构造函数
           Empty(const Empty& rhs) { ... }   // 复制(copy)构造函数
           ~Empty() { ... }                  // 析构函数
           Empty& operator=(const Empty& rhs) { ... } // copy assignment 操作符
     };

6.若不想使用编译器自动生成的函数，就该明确拒绝。
   为驳回编译器自动(暗自)提供的机能，可将相应的成员函数声明为 private 并且不予实现。
使用像 Uncopybale 这样的 base class 也是一种做法。
   class Uncopyable {
      protected:
         Uncopyable() { }                    // 允许 derived 对象构造和析构
         ~Uncopyable() { } 
      private:
         Uncopyable(const Uncopyable&);      // 阻止 copying
         Uncopyable& operator=(const Uncopyable&); 
   };
   class HomeForSale: private Uncopyable {   // 不在声明 copy 构造函数或 copy assign 操作符
      ...      
   };

7.为多态基类声明 virtual 析构函数。
  (1) polymorphic(带多态性质的) base classes 应该声明一个 virtual 析构函数。如果 class 带有任何 virtual 函数，
它就应该拥有一个 virtual 析构函数。
   class TimeKeeper {
      public:
        TimeKeeper();
        virtual ~TimeKeeper();
        ...
   };
   TimeKeeper* ptk = getTimeKeeper();
   ...
   delete ptk;
  (2) Classes 的设计目的如果不是作为 base classes 使用，或不是为了具备多态性(polymorphically)，就不应该
声明 virtual 析构函数。
    class AWOV {       // AWOV = "Abstract w/o Virtuals"
       public:
         virtual ~AWOV() = 0;      // 声明 pure virtual 析构函数
    };
    AWOV::~AWOV() { }  // pure virtual 析构函数的定义

8.别让异常逃离析构函数。
   (1) 析构函数绝对不要吐出异常。如果一个被析构函数调用的函数可能抛出异常，析构函数应该捕捉任何异常，然后吞下
它们(不传播)或结束程序。
   (2) 如果客户需要对某个操作函数运行期间抛出的异常做出反应，那么 class 应该提供一个普通函数(而非在析构函数中)
执行该操作。
    class DBConn {
       public:
         ...
         void close() {      // 供客户使用的新函数
            db.close();
            closed = true;
         }
         ~DBConn() {
            if (!closed) {
               try {    
                  db.close();      // 关闭连接(如果客户未关闭连接)
               } catch (...) {
                   // 记录下来并结束程序，或吞下异常
               }
            }
         }
       private:
         DBConnection db;
         bool closed;
    };

9.绝不在构造和析构过程中调用 virtual 函数。
   在构造和析构期间不要调用 virtual 函数，因为这类调用从不下降至 derived class(比起当前执行构造函数和析构函数
的那层)。
    class Transaction {
       public:
          explicit Transaction(const std::string& logInfo);
          void logTransaction(const std::string& logInfo) const;   // non-virtual 函数
          ...
    };
    Transaction::Transaction(const std::string& logInfo){
       ...
       logTransaction(logInfo);     // non-virtual 函数调用
    }
    class BuyTransaction : public Transaction {
       public:
          // 将 log 信息传给基类构造函数
          BuyTransaction(parameters) : Transaction(createLogString(parameters)) {  
             ...  
          }
          ...
       private:
         static std::string createLogString(parameters);
    };
```
