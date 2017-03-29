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
   如果你需要为某个函数的所有参数(包括被 this 指针所指的那个隐喻参数)进行类型转换，
那么这个函数必须是个非成员(non-member)函数。
   class Rational {
      ...        // 不包括 operator*
   };
   // non-member函数
   const Rational operator*(const Rational& lhs, const Rational& rhs) {
      return Rational(lhs.numerator() * rhs.numerator(), lhs.denominator() * rhs.denominator());
   }
   Rational oneFourth(1, 4);
   Rational result;
   result = oneFourth * 2;    // 没问题，通过编译
   result = 2 * oneFourth;    // 没问题，通过编译

25.考虑写出一个不抛异常的 swap 函数。
   (1) 当 std::swap 对你的类型效率不高时，提供一个 swap 成员函数，并确定这个函数不抛出异常。
   (2) 如果你提供一个 member swap，也该提供一个 no-member swap 用来调用前者。
对于 classes (而非 templates)，也请特化 std::swap。
   (3) 调用 swap 时应针对 std::swap 使用 using 声明式，然后调用 swap 并且不带任何"命名空间资格修饰"。
   (4) 为"用户定义类型"进行 std templates 全特化是好的，但千万不要尝试在 std 内加入某些对 std 而言全新的东西。
      namespace WidgetStuff {
         ...               // 模板化的 WidgetImpl
         template<typename T>        // 内含 swap 成员函数
         class Widget { ... };
         ...
         template<typename T>        // non-member swap 函数
         // 这里不属于 std 命名空间
         void swap(Widget<T>& a, Widget<T>& b) {
            a.swap(b);
         }
      }

26.尽可能延后变量定义式的出现时间。
   这样做可增加程序的清晰度并改善程序效率。
   定义并初始化 encrypted 的最佳做法。
     std::string encryptPassword(const std::string& password) {
        ...      // 检查长度
        std::string encrypted(password);    // 通过 copy 构造函数定义并初始化
        encrypt(encrypted);
        return encrypted;
     }

27.尽量少做转型动作。
   (1) 如果可以，尽量避免转型，特别是在注重效率的代码中避免 dynamic_casts。如果有个设计需要转型动作，
试着发展无需转型的替代设计。
   (2) 如果转型是必要的，试着将它隐藏于某个函数背后。客户随后可以调用该函数，而不需要将转型放进他们自己的代码内。
   (3) 宁可使用 C++ style(新式)转型，不要使用旧式转型。前者容易辨识出来，而且也比较有着分门别类的职责。
     "旧式转型"(old-style casts)：
        C风格：     (T) expression          // 将 expression 转型为 T
        函数风格：   T (expression)         // 将 expression 转型为 T
     新式转型(C++ style casts)：
        const_cast<T> (expression)         // 对象常量性转型
        dynamic_cast<T> (expression)       // 安全向下转型，用来决定某对象是否归属继承体系中的某个类型
        reinterpret_cast<T> (expression)   // 低级转型，不可移植
        static_cast<T> (expression)        // 强迫隐式转换
     dynamic_cast 替代方案：“使用类型安全容器”或"将 virtual 函数往继承体系上方移动"。
        class Window {
          public:
             virtual void blink() { }      // 缺省实现代码"什么也没做"
             ...
        };
        class SpecialWindow: public Window {
           public:
              virtual void blink() { ... }; // 在此 class 内，blink 做某些事
              ...
        };
        typedef std::vector<std::trl::shared_ptr<Window>> VPW;
        VPW winPtrs;           // 容器，内含指针，指向所有可能的 Window 类型
        ...
        for (VPW::iterator iter = winPtrs.begin(); iter != winPtrs.end(); ++iter)
           (*iter)->blink();   // 注意，这里没有 dynamic_cast

28.避免返回 handles 指向对象内部成分。
   避免返回 handles(包括 references、指针、迭代器) 指向对象内部。遵守这个条款可增加封装性，帮助 const 成员函数
的行为像个 const，并将发生"虚吊号码牌"(dangling handles) 的可能性降至最低。
     class Rectangle {
        public:
           ...
           const Point& upperLeft() const { return pData->ulhc; }
           const Point& lowerRight() const { return pData->lrhc; }
           ...
     };
     
29.为"异常安全"而努力是值得的。
   (1) 异常安全函数 (Exception-safe functions) 即使发生异常也不会泄漏资源或允许任何数据结构败坏。这样的函数
区分为三种可能的保证：基本型、强烈型、不抛异常型。
   (2) "强烈保证"往往能够以 copy-and-swap 实现出来，但"强烈保证"并非对所有函数都可实现或具备现实意义。
   (3) 函数提供的"异常安全保证"通常最高只等于其所调用之各个函数的"异常安全保证"中的最弱者。
     struct PMImpl {        // PMImpl = "PrettyMenu Impl"
         std::trl::shared_ptr<Image> bgImage;
         int imageChanges;
     };
     class PrettyMenu {
         ...
       private:
         Mutex mutex;
         std::trl::shared_ptr<PMImpl> pImpl;
     };
     void PrettyMenu::changeBackground(std::istream& imgSrc) {
        using std::swap;
        Lock ml(&mutex);     // 获得 mutex 的副本数据
        std::trl:shared_ptr<PMImpl> pNew(new PMImpl(*pImpl));
        pNew->bgImage.reset(new Image(imgSrc));    // 修改副本
        ++pNew->imageChanges;
        swap(pImpl, pNew);   // 置换(swap)数据，释放 mutex
     }
     
30.透彻了解 inlining 的里里外外。

```
