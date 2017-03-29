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
   (1) 切记将成员变量声明为 private。这可赋予客户访问数据的一致性、可细微划分访问控制、允诺约束条件获得保证，
并提供 class 作者以充分的实现弹性。
   (2) protected 并不比 public 更具封装性。

23.宁以非成员(non-member)、非友元(non-friend)函数替换成员(member)函数。
   这样做可以增加封装性、包裹弹性(packaging flexibility)和机能扩充性。
    namespace WebBrowserStuff {
       class WebBrowser { ... };
       void clearBrowser(WebBrowser& wb);
       ...
    }

24.若所有参数皆需类型转换，请为此采用非成员(non-member)函数。

```
