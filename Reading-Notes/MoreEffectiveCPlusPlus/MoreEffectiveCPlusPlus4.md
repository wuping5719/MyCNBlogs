<h2>《More Effective C++》 :books: </h2> 

> [美] Scott Meyers 著    电子工业出版社

```c++
31.让函数根据一个以上的对象类型来决定如何虚化。

32.在未来时态下发展程序。
   (1) 提供完整的 classes，即使某些部分目前用不到。
   (2) 设计你的接口，使有利于共同的操作行为，阻止共同的错误。
   (3) 尽量使你的代码一般化 (泛化)，除非有不良的巨大后果。
   
33.将非尾端类 (non-leaf classes) 设计为抽象类 (abstract classes)。
   class AbstractAnimal {
      protected:
         AbstractAnimal& operator=(const AbstractAnimal& rhs);
      public:
         virtual ~AbstractAnimal() = 0;
         ...
   };
   class Animal: public AbstractAnimal {
      public:
         Animal& operator=(const Animal& rhs);
         ...
   };

34.如何在同一个程序中结合 C++ 和 C。

35.让自己习惯标准 C++ 语言。
```
