<h2>《Effective C++》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
31.将文件间的编译依存关系将至最低。
   (1) 支持"编译依存性最小化"的一般构想是：相依于声明式，不要相依于定义式。
基于此构想的两个手段是 Handle classes 和 Interface classes。
   (2) 程序头文件应该以"完全且仅有声明式"(full and declaration-only forms)的形式存在。
这种做法不论是否涉及 templates 都适用。
    以"声明的依存性"替换"定义的依存性"：
    ① 如果使用 object references 或 object pointers 可以完成任务，就不要使用 objects。
    ② 如果能够，尽量以 class 声明式替换 class 定义式。
       class Date;                        // class 声明式
       void clearAppointments(Date d);    // Date 的定义式
    ③ 为声明式和定义式提供不同的头文件
      #include "datefwd.h"        // 声明(但未定义) Date 类
      void clearAppointments(Date d);

32.确定你的 public 继承塑模出 is-a 关系。
   "public 继承"意味着 is-a。适用于 base class 身上的每一件事情一定也适用于 derived class 身上，
因为每一个 derived class 对象也都是一个  base class 对象。

33.避免遮掩继承而来的名称。
   (1) derived class 内的名称会遮掩 base class 内的名称。在 public 继承下从来没有人希望如此。
   (2) 为了让被遮掩的名称再见天日，可使用 using 声明式或转交函数(forwarding function)。
      class Base {
         public:
            virtual void mf1() = 0;
            virtual void mf1(int);
            ...
      };
      class Derived: private Base {
         public:
            virtual void mf1() {
               Base::mf1();     // 转交函数(forwarding function)暗自成为 inline
            }
            ...
      };
      ...
      Derived d;
      int x;
      d.mf1();     // 很好，调用 Derived::mf1
      d.mf1(x);    // 错误！Base::mf1()被遮掩了

34.区分接口继承和实现继承。
```
