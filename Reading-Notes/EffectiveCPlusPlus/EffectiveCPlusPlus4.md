<h2>《Effective C++》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
31.将文件间的编译依存关系将至最低。
   (1) 支持"编译依存性最小化"的一般构想是：相依于声明式，不要相依于定义式。
基于此构想的两个手段是 Handle classes 和 Interface classes。
   (2) 程序头文件应该以"完全且仅有声明式"(full and declaration-only forms)的形式存在。
这种做法不论是否涉及 templates 都适用。

32.确定你的 public 继承塑模出 is-a 关系。

33.避免遮掩继承而来的名称。
```
