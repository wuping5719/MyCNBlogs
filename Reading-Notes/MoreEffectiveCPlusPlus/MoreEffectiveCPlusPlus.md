<h2>《More Effective C++》 :books: </h2> 

> [美] Scott Meyers 著    电子工业出版社

```c++
1.仔细区别 pointers 和 references。
  pointers 使用 “ * ” 和 “ -> ” 操作符，references 使用 “ . ”。
  当你知道你需要指向某个东西，而且绝不会改变指向其他东西，或是当你实现一个操作符而其语法需求无法由 pointers 达成，
你就应该选择 references。任何其它时候，请采用 pointers。

2.最好使用 C++ 转型操作符。
  (1) 可以用 static_cast<type>(expression) 替换 (type) expression。
  (2) 使用 const_cast 将某个对象的常量性去掉。
  (3) dynamic_cast 用来执行继承体系中 “安全的向下转型或跨系转型动作”，可以利用它将 “指向 base class objects
的指针或引用” 转型为 “指向 derived(或 sibling base) class objects 的指针或引用”。
  (4) reinterpret_cast 用于转换 “函数指针” 类型。

3.绝对不要以多态 (polymorphically) 方式处理数组。
```
