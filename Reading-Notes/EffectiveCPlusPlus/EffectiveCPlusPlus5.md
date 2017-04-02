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
   
44.将与参数无关的代码抽离 template。
   (1) Template 生成多个 class 和多个函数，所以任何 template 代码都不应该与某个造成膨胀的 template 函数
产生相依关系。
   (2) 因非类型模版参数 (non-type template parameters) 而造成的代码膨胀，往往可以消除，做法是以函数参数
或 class 成员变量替换 template 参数。
    ① 方法一：建立一个带数值参数的函数。
    template<typename T>                   
    class SquareMatrixBase {                      // 与尺寸无关的基类，用于正方矩阵
       protected:
          ...
          void invert(std::size_t matrixSize);    // 以给定尺寸求逆矩阵
    };
    template<typename T, std::size_t n>
    class SquareMatrix: private SquareMatrixBase<T> {
       private:
          using SquareMatrixBase<T>::invert;      // 避免遮掩基类的 invert
       public:
          ...
          void invert() { this->invert(n); }      // 制造一个 inline 调用，调用基类的 invert
    };
    ② 方法二：贮存一个指针，指向矩阵数值所在的内存。
    template<typename T>                   
    class SquareMatrixBase {                     
       protected:
          // 存储矩阵大小和一个指针，指向矩阵数值
          SquareMatrixBase(std::size_t n, T* pMem) : size(n), pData(pMem) { }
          void setDataPtr(T* ptr) { pData = ptr; }   // 重新赋值给 pData
          ...
       private:
          std::size_t size;           // 矩阵大小
          T* pData;                   // 指针，指向矩阵内容
    };
    template<typename T, std::size_t n>
    class SquareMatrix: private SquareMatrixBase<T> {
       public:
          // 将基类的数据指针设为 null，为矩阵内容分配内存，将指向该内存的指针存储起来，然后将它的一个副本交给基类
          SquareMatrix() : SquareMatrixBase<T>(n, 0), pData(new T[n*n]) { this->setDataPtr(pData.get()); }
          ...
       private:
          boost::scoped_array<T> pData;
    };
   (3) 因类型参数 (type parameters) 而造成的代码膨胀，往往可降低，做法是让带有完全相同的二进制表述
(binary respresentations) 的具现类型 (instantiation types) 共享实现码。

45.运用成员函数模版接受所有兼容类型。
   (1) 请使用 member function template (成员函数模版) 生成 “可接受所有兼容类型” 的函数。
      template<typename T>
      class SmartPtr {
         public:
            template<typename U>
            // 以 other 的 heldPtr 初始化 this 的 heldPtr
            SmartPtr(const SmartPtr<U>& other) : heldPtr(other.get()) { ... } 
            T* get() const { return heldPtr; }
            ...
         private:
            T* heldPtr;   // 这个 SmartPtr 持有的内置 (原始) 指针
      };
   (2) 如果你声明 member template 用于 "泛化 copy 构造" 或 "泛化 assignment 操作"，
你还是需要声明正常的 copy 构造函数和 copy assignment 操作符。
      template<class T>
      class shared_ptr {
         public:
            shared_ptr(shared_ptr const& r);       // copy 构造函数
            template<class Y>                      // 泛化 copy 构造函数
            shared_ptr(shared_ptr<Y> const& r); 
            shared_ptr& operator=(shared_ptr const& r);   // copy assignment
            template<class Y>                             // 泛化 copy assignment
            shared_ptr& operator=(shared_ptr<Y> const& r); 
            ...
      };
      
46.需要类型转换时请为模版定义非成员函数。
   当我们编写一个 class template，而它所提供之 "与此 template 相关的" 函数支持 "所有参数之隐式类型转换" 时，
请将那些函数定义为 "class template 内部的 friend 函数"。
     template<typename T> class Rational;       // 声明 Rational template
     template<typename T>                       // 声明 helper template
     const Rational<T> doMultiply(const Rational<T>& lhs, const Rational<T>& rhs);
     template<typename T> 
     class Rational {
        public:
           ...
           // 令 friend 调用 helper
           friend const Rational<T> operator*(const Rational<T>& lhs, const Rational<T>& rhs) {
               return doMultiply(lhs, rhs);
           }
           ...
     };
     template<typename T>     // 若有必要，在头文件内定义 helper template
     const Rational<T> doMultiply(const Rational<T>& lhs, const Rational<T>& rhs) {
        return Rational<T>(lhs.numerator() * rhs.numerator(), lhs.denominator() * rhs.denominator());
     }
     
47.请使用 traits classes 表现类型信息。
   (1) Traits classes 使得 "类型相关信息" 在编译期可用。它们以 templates 和 "templates 特化" 完成实现。
      struct input_iterator_tag {};      // Input 迭代器只能向前移动，一次一步，只能读取 (istream_iterator)
      struct output_iterator_tag {};     // Output 迭代器只能向前移动，一次一步，只能涂写 (ostream_iterator)
      struct forward_iterator_tag: public input_iterator_tag {};  // Forward 迭代器只能向前移动，可读写 (slist)
      // Bidirectional 迭代器可以前后移动，可读写 (list，set，multiset，map，multimap)
      struct bidirectional_iterator_tag: public forward_iterator_tag {};  
      // Random Access 迭代器可以在常量时间内向前或向后跳跃任意距离，可读写 (vector，deque，string)
      struct random_access_iterator_tag: public bidirectional_iterator_tag {};  
   (2) 整合重载技术 (overloading) 后，traits classes 有可能在编译期对类型执行 if...else 测试。
      template<typename IterT, typename DistT>   // 这份实现用于 random access 迭代器
      void doAdvance(IterT& iter, DistT d, std::random_access_iterator_tag) {
         iter += d;
      }
      template<typename IterT, typename DistT>   // 这份实现用于 bidirectional 迭代器
      void doAdvance(IterT& iter, DistT d, std::bidirectional_iterator_tag) {
         if (d >= 0) { while (d--) ++iter; }
         else { while (d++) --iter; }
      }
      template<typename IterT, typename DistT>   // 这份实现用于 input 迭代器
      void doAdvance(IterT& iter, DistT d, std::input_iterator_tag) {
         if (d < 0) { throw std::out_of_range("Negative distance"); }
         while (d--) ++iter; 
      }
      template<typename IterT, typename DistT>
      void advance(IterT& iter, DistT d) {
         // 调用的 doAdvance 版本对 iter 之迭代器分类而言必须是适当的
         doAdvance(iter, d, typename std::iterator_traits<IterT>::iterator_category());
      }

48.认识 template 元编程。
```
