<h2>《Effective C++》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
41.了解隐式接口和编译期多态。
  (1) class 和 template 都支持接口 (interface) 和多态 (polymorphism)。
  (2) 对 class 而言接口是显示的 (explicit)，以函数签名为中心。多态则是通过 virtual 函数发生于运行期。
      通常显示接口由函数的签名式(函数名称、参数类型、返回类型)构成。
  (3) 对 template 参数而言，接口是隐式的 (implicit)，奠基于有效表达式。多态则是通过 template 具现化
和函数重载解析 (function overloading resolution) 发生于编译器。   

42.了解 typename 的双重意义。
  (1) 声明 template 参数时，前缀关键字 class 和 typename 可互换。
      template<class T> class Widget;
      template<typename T> class Widget;
  (2) 请使用关键字 typename 标识嵌套从属类型名称；但不得在 base class list (基类列表)
或 member initialization list (成员初始化列表) 内以它作为 base class 修饰符。
      template<typename T>
      class Derived: public Base<T>::Nested {  // base class list 中不允许 "typename"
         public:
            explicit Derived(int x) : Base<T>::Nested(x) {    // 成员初始化列表 中不允许 "typename"
               typename Base<T>::Nested temp;  // 嵌套从属类型名称，作为一个 base class 修饰符需加上 typename
               ...
            }
            ...
      };

43.学习处理模板化基类内的名称。
   可在 derived class template 内通过 "this->" 指涉 base class template 内的成员名称，
或藉由一个明白写出的 "base class 资格修饰符" 完成。
   template<>      // 一个全特化的 MsgSender, 它和一般 template 相同，差别只在于它删掉了 sendClear
   class MsgSender<CompanyZ> {
      public:
        ...
        void sendSecret(const MsgInfo& info) { ... }
   };
   template<typename Company>
   class LoggingMsgSender: public MsgSender<Company> {
      public:
        ...
        void sendClearMsg(const MsgInfo& info) {
          this->sendClear(info);
        }
        ...
   }
   template<typename Company>
   class LoggingMsgSender: public MsgSender<Company> {
      public:
        using MsgSender<Company>::sendClear;     // 告诉编译器，请它假设 sendClear 位于 base class 内
        ...
        void sendClearMsg(const MsgInfo& info) {
           ...
           sendClear(info);
           ...
        }
        ...
   }
 
```
