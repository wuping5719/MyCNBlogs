<h2>《More Effective C++》 :books: </h2> 

> [美] Scott Meyers 著    电子工业出版社

```c++
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
    };
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
30.Proxy classes (替身类、代理类)。
   Proxy classes 允许我们完成某些十分困难或几乎不可能完成的行为。多维数组是其中之一，左值/右值的区分
是其中之二，压抑隐式转换是其中之三。
   (1) 实现二维数组：
    template<class T>
    class Array2D {
       public:
         class Array1D {
            public:
               T& operator[](int index);
               const T& operator[](int index) const;
               ...
         };
         Array1D operator[](int index);
         const Array1D operator[](int index) const;
         ...
    };
   (2) 区分 operator[] 的读写动作：
    class String {
       public:
          class CharProxy {       // proxies for string chars
             public:
                CharProxy(String& str, int index);              // 构造
                CharProxy& operator=(const CharProxy& rhs);     // 左值运用
                CharProxy& operator=(char c);
                operator char() const                           // 右值运用
             private:
                String& theString;          // proxy 附属的字符串
                int charIndex;              // proxy 所代表的字符串字符
          };
          const CharProxy operator[](int index) const;        // 针对 const Strings
          CharProxy operator[](int index);                    // 针对 non-const Strings
          ...
        friend class CharProxy;
       private:
          RCPtr<StringValue> value;
    };
```
