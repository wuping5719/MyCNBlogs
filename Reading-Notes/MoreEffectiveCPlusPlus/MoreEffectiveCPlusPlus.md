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
  多态 (polymorphism) 和指针算术不能混用。数组对象几乎总是会涉及指针的算术运算，所以数组和多态不要混用。

4.非必要不提供默认构造函数 (default constructor)。
  针对公司仪器设计的 class，其中仪器识别码是一定得有的一个 constructor 自变量：
    class EquipmentPiece {
       public:
          EquipmentPiece(int IDNumber);
          ...
    };
    // 分配足够的 raw memory，给一个预备容纳 10 个 EquipmentPiece objects 的数组使用
    void *rawMemory = operator new[](10 * sizeof(EquipmentPiece));
    // 让 bestPieces 指向此块内存，使这块内存被视为一个 EquipmentPiece 数组
    EquipmentPiece *bestPieces = static_cast<EquipmentPiece*>(rawMemory);
    // 利用 “placement new” 构造这块内存中的 EquipmentPiece objects
    for (int i = 0; i < 10; ++i)
       new (&bestPieces[i]) EquipmentPiece(IDNumber);
    // 将 bestPieces 中的各个对象，以其构造顺序的相反顺序析构掉
    for (int i = 9; i >= 0; --i)
       bestPieces[i].~EquipmentPiece();
    // 释放 raw memory
    operator delete[](rawMemory);

5.对定制的 “类型转换函数” 保持警觉。
  允许编译器执行隐式类型转换，害处多过好处。所以不要提供转换函数，除非你确定你需要它们。

6.区别 increment / decrement 操作符的前置 (prefix) 和后置 (postfix) 形式。
    class UPInt {                        // "unlimited precision int"
       public:
          UPInt& operator++();           // 前置式 (prefix) ++
          const UPInt operator++(int);   // 后置式 (postfix) ++
          UPInt& operator--();           // 前置式 (prefix) --
          const UPInt operator--(int);   // 后置式 (postfix) --
          UPInt& operator+=(int)         // +=操作符，结合 UPInt 和 int
          ...
    };
    // 前置式：累加然后取出 (increment and fetch)
    UPInt& UPInt::operator++() {
       *this += 1;      // 累加 (increment)
       return *this;    // 取出 (fetch)
    }
    // 后置式：取出然后累加 (fetch and increment)
    const UPInt UPInt::operator++(int) {
       UPInt oldValue = *this;   // 取出 (fetch)
       ++(*this);                // 累加 (increment)
       return oldValue;          // 返回先前被取出的值
    }

7.千万不要重载 &&，|| 和 逗号(,)操作符。

8.了解各种不同意义的 new 和 delete。

9.利用 destructors 避免泄漏资源。

10.在 constructors 内阻止资源泄漏 (resource leak)。
```
