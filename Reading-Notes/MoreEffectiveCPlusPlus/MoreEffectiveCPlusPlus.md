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
   如果你希望将对象产生于 heap，请使用 new operator。它不但分配内存而且为该对象调用一个 constructor。
如果你只是打算分配内存，请调用 operator new，那就没有任何 constructor 会被调用。如果你打算在 heap objects 
产生时自己决定内存分配方式，请写一个自己的 operator new，并使用 new operator，它将会自动调用你所写的 operator new。
如果你打算在已分配(并拥有指针)的内存中构造对象，请使用 placement new。
   new operator 和 delete operator 都是内建操作符，无法为你控制。
  
9.利用 destructors 避免泄漏资源。
   class ALA {         // ALA：Adorable Little Animal
      public:
         virtual void processAdoption() = 0;
         ...
   };
   class Puppy: public ALA {
      public:
         virtual void processAdoption();
         ...
   };
   // ALA * readALA(istream& s);   //  从 s 读取动物信息，然后返回一个指针，指向一个新分配的对象，有着适当的类型
   void processAdoptions(istream& dataSource) {
      while (dataSource) {
         auto_ptr<ALA> pa(readALA(dataSource));
         pa->processAdoption();
      }
   }
  
10.在 constructors 内阻止资源泄漏 (resource leak)。
   如果你以 auto_ptr 对象来取代 pointer class members，你便对你的 constructors 做了强化工事，
免除了 “exception 出现时发生资源泄漏” 的危机，不再需要在 destructors 内亲自手动释放资源，
并允许 const member pointers 得以和 non-const member pointers 有着一样优雅的处理方式。
   class BookEntry {     // 用来放置通信薄的每一个个人数据
      public:
         BookEntry(const string& name, const string& address = "",
                   const string& imageFileName = "", const string& audioClipFileName = "");
         ~BookEntry();
         // 电话号码通过此函数加入
         void addPhoneNumber(const PhoneNumber& number);
         ...
      private:
         string theName;                 // 个人姓名
         string theAddress;              // 个人地址
         list<PhoneNumber> thePhones;    // 个人电话号码
         const auto_ptr<Image> theImage;                // 个人相片
         const auto_ptr<AudioClip> theAudioClip;        // 一段个人声音
         
   };
   BookEntry::BookEntry(const string& name, const string& address, 
                        const string& imageFileName, const string& audioClipFileName) 
                  : theName(name), theAddress(address),
                    theImage(imageFileName != "" ? new Image(imageFileName) : 0),
                    theAudioClip(audioClipFileName != "" ? new AudioClip(audioClipFileName) : 0)
   {}

11.禁止异常 (exception) 流出 destructors 之外。
   (1) 可以避免 terminate 函数在 exception 传播过程的栈展开 (stack-unwinding) 机制中被调用。
   (2) 可以协助确保 destructors 完成其应该完成的所有事情。
    Session::~Session() {
       try {
          logDestruction(this);
       } catch (...) { }
    }

12.了解 “抛出一个 exception” 与 “传递一个参数” 或 “调用一个虚函数” 之间的差异。
  “传递对象到函数去，或是以对象调用虚函数” 和 “将对象抛出成为一个 exception” 之间，有 3 个主要差异：
   (1) exception objects 总是会被复制，如果以 by value 方式捕捉，它们甚至被复制两次。至于传递给函数参数的
对象则不一定得复制。
   (2) “被抛出成为 exceptions” 的对象，其被允许的类型转换动作，比 “被传递到函数去” 的对象少。
   (3) catch 子句以其 “出现于源代码的顺序” 被编译器检验比对，其中第一个匹配成功者便执行；而当我们以某对象调用
一个虚函数，被选中执行的是那个 “与对象类型最佳吻合” 的函数，不论它是不是源代码所列的第一个。
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_exceptions.png" />

```c++
13.以 by reference 方式捕捉 exceptions。
   void doSomething() {
      try {
         someFunction();
      } catch (exception& ex) {
         cerr << ex.what();     // 调用 Validation_error::what() 而非 exception::what()
         ...
      }
   }

14.明智运用 exception specifications。
   所有非预期的 exceptions 都以 UnexpectedException objects 取而代之。
   class UnexpectedException {};     
   void convertUnexpected() {   // 如果有一个非预期的 exception 被抛出，便调用此函数
       throw UnexpectedException();
   }
   set_unexpected(convertUnexpected);  // convertUnexpected 取代默认的 unexpected 函数

15.了解异常处理 (exception handling) 的成本。
   使用 try 语句块，代码大约整体膨胀 5% ~ 10%，执行速度亦大约下降这个数。
   
16.谨记 80 - 20 法则。
   80 - 20 法则：一个程序 80% 的资源用于 20% 的代码身上。

17.考虑使用 lazy evaluation (缓式评估)。
   lazy evaluation 在许多领域中都可能有用途：可避免非必要的对象复制，可区别 operator[] 的读取和写动作，
可避免非必要的数据库读取动作，可避免非必要的数值计算动作。

18.分期摊还预期的计算成本。
   可以通过超急评估 (over-eager evaluation) 如缓存 (caching) 和 预先取出 (prefetching) 
等做法分期摊还预期运算成本。
    int findCubicleNumber(const string& employeeName) {
        // 定义一个 static map 以持有 (employee name, cubicle number) 数据对。这个 map 用来做局部缓存
        typedef map<string, int> CubicleMap;
        static CubicleMap cubes;
        // 尝试在 cache 中针对 employeeName 找出一笔记录。如果确有一笔， 迭代器便会指向该笔找到的记录
        CubicleMap::iterator it = cubes.find(employeeName);
        // 如果找不到任何吻合记录，迭代器的值将会是 cubes.end()。如果这样，则查询数据库，然后把它加进 cache 中
        if (it == cubes.end()) {
           int cubicle = the result of looking up employeeName`s cubicle number int the database;
           cubes[employeeName] = cubicle;
           return cubicle;
        } else {
           // 迭代器指向正确的 cache 记录，只需获取 (employee name, cubicle number) pair 的第二项数据
           return (*it).second;
        }
    }

19.了解临时对象的来源。
   任何时候只要你看到一个 reference-to-const 参数，就极可能会有一个临时对象被产生出来绑定至该参数上。
任何时候只要你看到函数返回一个对象，就会产生临时对象(并于稍后销毁)。

20.协助完成 “返回值优化(RVO)”。
   // 函数返回一个对象：最有效率的做法
   inline const Rational operator*(const Rational& lhs, const Rational& rhs) {
      return Rational(lhs.numerator() * rhs.numerator(), lhs.denominator() * rhs.denominator());
   }

21.利用重载技术 (overload) 避免隐式类型转换 (implicit type conversions)。
   任何函数如果接受类型为 string，char*，complex 等自变量，都可以借由重载技术，合理消除类型转换。
   const UPInt operator+(const UPInt& lhs, const UPInt& rhs);  // 将 UPInt 和 UPInt 相加
   const UPInt operator+(const UPInt& lhs, int rhs);           // 将 UPInt 和 int 相加
   const UPInt operator+(int lhs, const UPInt& rhs);           // 将 int 和 UPInt 相加

22.考虑以操作符复合形式 (op=) 取代其独身形式 (op)。
   template<class T>
   const T operator+(const T& lhs, const T& rhs) {
      return T(lhs) += rhs;
   }
   template<class T>
   const T operator-(const T& lhs, const T& rhs) {
      return T(lhs) -= rhs;
   }

23.考虑使用其他程序库。
   #ifdef STDIO
   #include <stdio.h>
   #else
   #include <iostream>
   #include <iomanip>
   using namespace std;
   #endif
   const int VALUES = 30000;  // 读写的数值个数
   int main() {
      double d;
      for (int n = 1; n <= VALUES; ++n) {
   #ifdef STDIO
         scanf("%lf", &d);
         printf("%10.5f", d);
   #else
         cin >> d;
         cout << setw(10)                      // 设定字段宽度
              << setprecision(5)               // 设定浮点数的精度
              << setiosflags(ios::showpoint)   // 显示浮点数的小数点
              << setiosflags(ios::fixed)       // 以十进制显示浮点数
              << d;
   #endif
         if (n % 5 == 0) {
   #ifdef STDIO
             printf("\n");
   #else
             cout << '\n'; 
   #endif 
         }
      }
      return 0;
   }

24.了解虚函数(virtual functions)、多重继承(multiple inheritance)、虚拟基类(virtual base classes)、
运行时期类型辨识(runtime type identification)的成本。
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_RTTI.png" />

```c++
25.将 constructor 和 non-member functions 虚化。
   class NLComponent {
      public:
          // 声明 virtual copy constructor
          virtual NLComponent * clone() const = 0;
          virtual ostream& print(ostream& s) const = 0;
          ... 
   };
   class TextBlock: public NLComponent {
       public:
          virtual TextBlock * clone() const {
             return new TextBlock(*this);
          }
          virtual ostream& print(ostream& s) const;
          ...
   };
   inline ostream& operator<<(ostream& s, const NLComponent& c) {
       return c.print(s);
   }

26.限制某个 class 所能产生的对象数量。
   一个用来计算对象个数的基类：
   template<class BeingCounted>
   class Counted {
      public:
         class TooManyObject {};  // 可能抛出的异常
         static int objectCount() { return numObjects; }
      protected:
         Counted();
         Counted(const Counted& rhs);
         ~Counted() { --numObjects; }
      private:
         static int numObjects;
         static const size_t maxObjects;
         void init();
   };
   template<class BeingCounted>
   Counted<BeingCounted>::Counted() { init(); }
   template<class BeingCounted>
   Counted<BeingCounted>::Counted(const Counted<BeingCounted>&) { init(); }
   template<class BeingCounted>
   void Counted<BeingCounted>::init() {
      if (numObjects >= maxObjects) throw TooManyObject();
      ++numObjects;
   }
   让 Printer 类运用 Counted 模版：
   class Printer: private Counted<Printer> {
      public:
         // 伪构造函数 (pseudo-constructors)
         static Printer * makePrinter();
         static Printer * makePrinter(const Printer& rhs);
         ~Printer();
         void submitJob(const PrintJob& job);
         void reset();
         void performSelfTest();
         ...
         using Counted<Printer>::objectCount;
         using Counted<Printer>::TooManyObjects;
      private:
         Printer();
         Printer(const Printer& rhs);
   };

27.要求(或禁止)对象产生于 heap 之中。
   判断某指针是否以 operator new 分配出来：
   class HeapTracked {           // mixin class: 追踪并记录被 operator new 返回的指针
      public:
         class MissingAddress {};    
         virtual ~HeapTracked() = 0;
         static void *operator new(size_t size);
         static void operator delete(void *ptr);
         bool isOnHeap() const;
      private:
         typedef const void* RawAddress;
         static list<RawAddress> addresses;
   };
   list<RawAddress> HeapTracked::addresses;     // static class member 的义务性定义
   // HeapTracked 的 destructor 是个纯虚函数，使这个 class 成为抽象类
   HeapTracked::~HeapTracked() {}          
   void * HeapTracked::operator new(size_t size) {
      void *memPtr = ::operator new(size);      // 取得内存地址
      addresses.push_front(memPtr);             // 将其地址置于 list 头部
      return memPtr;
   }      
   void HeapTracked::operator delete(void *ptr) {
      // 获得一个 “iterator”，用以找出哪一笔 list 元素内含 ptr
      list<RawAddress>::iterator it = find(addresses.begin(), addresses.end(), ptr);
      if (it != addresses.end()) {   // 如果找到符合条件的元素
         addresses.erase(it);        // 移除之，并释放内存
         ::operator delete(ptr);
      } else {                       // 否则表示 ptr 不是 operator new 所分配，抛出一个异常
         throw MissingAddress();
      }
   }  
   bool HeapTracked::isOnHeap() const {
      // 取得一个指针，指向 *this 所占内存的起始处
      const void *rawAddress = dynamic_cast<const void*>(this);
      // 在地址链表中查找 “被 operator new 返回的指针”
      list<RawAddress>::iterator it = find(addresses.begin(), addresses.end(), rawAddress);
      return it != addresses.end();     // 返回 “找到与否” 信息
   }

28.Smart Pointers (智能指针)。
  某些 smart pointers 的用途受到诸如 “nullness 的测试、C++的内建指针(dumb pointers)的转换、以继承为本的转换、
对 pointers-to-consts 的支持”等限制。
  auto_ptr 的实现代码：
    template<class T>
    class auto_ptr {
       public:
          explicit auto_ptr(T *p = 0): pointee(p) {}
          template<class U>
          auto_ptr(auto_ptr<U>& rhs): pointee(rhs.release()) {}
          ~auto_ptr() { delete pointee; }
          template<class U>
          auto_ptr<T>& operator=(auto_ptr<U>& rhs) {
             if (this != &rhs) reset(rhs.release());
             return *this;
          }
          T& operator*() const { return *pointee; }
          T* operator->() const { return pointee; }
          T* get() const { return pointee; }
          T* release() { 
             T *oldPointee = pointee;
             pointee = 0;
             return oldPointee;
          }
          void reset(T *p = 0) {
             if (pointee != p) {
                delete pointee;
                pointee = p;
             }
          }
       private:
          T *pointee;
       template<class U> friend class auto_ptr<U>;
    };

29.引用计数(Reference counting)。
   以具有复用性质的 RCObject 和 RCPtr classes 为基础，建造一个 reference-counted String classes。
    template<class T>      // template class，用来产生 smart pointers-to-Tobjects。
    class RCPtr {          // T 必须继承自 RCObject
       public:
          RCPtr(T* realPtr = 0);
          RCPtr(const RCPtr& rhs);
          ~RCPtr();
          RCPtr& operator=(const RCPtr& rhs);
          T* operator->() const;
          T& operator*() const;
       private:
          T *pointee;
          void init();
    };
    template<class T> 
    void RCPtr<T>::init() { 
       if (pointee == 0) return;
       if (pointee->isShareable() == false) {
          pointee = new T(*pointee);
       }
       pointee->addReference();
    }
    template<class T> 
    RCPtr<T>::RCPtr(T* realPtr): pointee(realPtr) { init(); }
    template<class T> 
    RCPtr<T>::RCPtr(const RCPtr& rhs): pointee(rhs.pointee) { init(); }
    template<class T> 
    RCPtr<T>::~RCPtr() { if (pointee) pointee->removeReference(); }
    template<class T> 
    RCPtr<T>& RCPtr<T>::operator=(const RCPtr& rhs) {
       if (pointee != rhs.pointee) {
          if (pointee) pointee->removeReference();
          pointee = rhs.pointee;
          init();
       }
       return *this;
    }
    template<class T> 
    T* RCPtr<T>::operator->() const { return pointee; }
    template<class T> 
    T& RCPtr<T>::operator*() const { return *pointee; }
    // base class，用于 reference-counted Object
    class RCObject {      
        public:
           void addReference();
           void removeReference();
           void markUnshareable();
           bool isSharedable() const;
           bool isShared() const;
        protected:
           RCObject();
           RCObject(const RCObject& rhs);
           RCObject& operator=(const RCObject& rhs);
           virtual ~RCObject() = 0;
        private:
           int refCount;
           bool shareable;
    };
    RCObject::RCObject(): refCount(0), shareable(true) {}
    RCObject::RCObject(const RCObject&): refCount(0), shareable(true) {}
    RCObject& RCObject::operator=(const RCObject&) { return *this; }
    RCObject::~RCObject() {}
    void RCObject::addReference() { ++refCount; } 
    void RCObject::removeReference() { if (--refCount == 0) delete this; }
    void RCObject::markUnshareable() { shareable = false; }
    bool RCObject::isSharedable() const { return shareable; }
    bool RCObject::isShared() const { return refCount > 1; }
    // 应用性 class
    class String {
       public:
          String(const char *value = "");
          const char& operator[](int index) const;
          char& operator[](int index);
       private:
          // 以下 struct 用以表现字符串实值
          struct StringValue: public RCObject {
             char *data;
             StringValue(const char *initValue);
             StringValue(const StringValue& rhs);
             void init(const char *initValue);
             ~StringValue();
          };
          RCPtr<StringValue> value;
    }
    void String::StringValue::init(const char *initValue) {
       data = new char[strlen(initValue) + 1];
       strcpy(data, initValue);
    }
    String::StringValue::StringValue(const char *initValue) { init(initValue); }
    String::StringValue::StringValue(const StringValue& rhs) { init(rhs.data); }
    String::StringValue::~StringValue() { delete [] data; }
    String::String(const char *initValue): value(new StringValue(initValue)) {}
    const char& String::operator[](int index) const { return value->data[index]; }
    char& String::operator[](int index) {
       if (value->isShared()) {
          value = new StringValue(value->data);
       }
       value->markUnshareable();
       return value->data[index];
    }
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_ReferenceCount.png" />

```c++
```
