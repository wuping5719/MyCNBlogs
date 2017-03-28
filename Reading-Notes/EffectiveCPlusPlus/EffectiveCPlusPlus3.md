<h2>《Effective C++》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
21.必须返回对象时，别妄想返回其 reference。
   绝不要返回 pointer 或 reference 指向一个 local stack 对象，或返回 reference 指向一个 heap-allocated 
对象，或返回 pointer 或 reference 指向一个 local static 对象而有可能同时需要多个这样的对象。
   一个"必须返回新对象"的函数的正确写法：
     inline const Rational operator * (const Rational& lhs, const Rational& rhs) {
        return Rational(lhs.n * rhs.n, lhs.d * rhs.d);
     }

22.将成员变量声明为 private。
```
