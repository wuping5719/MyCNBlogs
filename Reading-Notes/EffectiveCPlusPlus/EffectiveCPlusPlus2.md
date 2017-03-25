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
```
