<h2>《Effective C++》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
11.在 operator= 中处理"自我赋值"。
   (1) 确保当对象自我赋值时 operator= 有良好行为。其中技术包括比较"来源对象"和"目标对象"的地址、
精心周到的语句顺序、以及 copy-and-swap。
   让 operator= 具备"异常安全性"往往自动获得"自我赋值安全"的回报。
     Widget& Widget::operator=(const Widget& rhs) {
         Bitmap* pOrig = pb;          // 记录原先的 pb
         pb = new Bitmap(*rhs.pb);    // 令 pb 指向 *pb 的一个副本
         delete pOrig;                // 删除原先的 pb
         return *this;
     }
   copy-and-swap 技术:
     class Widget {
        ...
        void swap(Widget& rhs);   // 交换 *this 和 rhs 的数据
        ...
     };
     Widget& Widget::operator=(const Widget& rhs) {
        Widget temp(rhs);         // 为 rhs 数据制作一份副本
        swap(temp);               // 将 *this 数据和上述副本的数据交换
        return *this;
     }
   (2) 确定任何函数如果操作一个以上的对象，而其中多个对象是同一个对象时，其行为仍然正确。

12.复制对象时勿忘其每一个成分。
   (1) copying 函数应该确保复制"对象内的所有成员变量"及"所有 base class 成分"。
   (2) 不要尝试以某个 copying 函数实现另一个 copying 函数。应该将共同机能放进第三个函数中，并由两个
copying 函数共同调用。

13.以对象管理资源。
   常见的系统资源：动态分配的内存、文件描述器(file descriptors)、互斥锁(mutex locks)、图形界面中的字型和笔刷、
数据库连接、以及网络 sockets。
   (1) 为防止资源泄漏，请使用 RAII (资源取得时机便是初始化时机，Resource Acquisition Is Initialization) 对象，
它们在构造函数中获得资源并在析构函数中释放资源。
   (2) 两个常被使用的 RAII classes 分别是 tr1::shared_ptr 和 auto_ptr。前者通常是较佳选择，因为其 copy 行为
比较直观。若选择 auto_ptr，复制动作会使它(被复制物)指向 null。
     void f1() {
        std::auto_ptr<Investment> pInv(createInvestment());  // 经由 auto_ptr 的析构函数自动删除 pInv
        ...
     }
     void f2() {
        // 经由 shared_ptr 的析构函数自动删除 pInv
        std::tr1::shared_ptr<Investment> pInv(createInvestment());  
        ...
     }
```
