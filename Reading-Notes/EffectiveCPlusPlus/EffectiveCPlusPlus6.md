<h2>《Effective C++》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
51.编写 new 和 delete 时需固守常规。
   (1) operator new 应该内含一个无穷循环，并在其中尝试分配内存，如果它无法满足内存需求，就该调用 new-handler。
它应该有能力处理 0 bytes 申请。Class 专属版本则还应该处理 “比正确大小更大的(错误)申请”。
    non-member operator new 伪码 (pseudocode):
      void* operator new(std::size_t size) throw(std::bad_alloc) {
         using namespace std;     
         if (size == 0) {   // 处理 0-byte 申请
            size = 1;       // 将它视为 1-byte 申请
         }
         while (true) {
            尝试分配 size bytes;
            if (分配成功)
            return (一个指针，指向分配来的内存);
            // 分配失败: 找出目前的 new-handing 函数
            new_handler globalHandler = set_new_handler(0);
            set_new_handler(globalHandler);
            if (globalHandler) (*globalHandler)();
            else throw std::bad_alloc();
         }
      }
   (2) operator delete 应该在收到 null 指针时不做任何事。Class 专属版本则还应该处理 “比正确大小更大的(错误)申请”。
     non-member operator delete 伪码 (pseudocode):
       void operator delete(void* rawMemory) throw() {
          if (rawMemory == 0) return;   // 如果将被删除的是个 null 指针，那就什么也不做
          现在，归还 rawMemory 所指的内存;
       }
      class Base {
        public:
           static void* operator new(std::size_t size) throw(std::bad_alloc);
           static void operator delete(void* rawMemory, std::size_t size) throw();
           ...
      };
      void* Base::operator new(std::size_t size) throw(std::bad_alloc) {
         if (size != sizeof(Base))   // 如果大小错误，令标准的 operator new 去处理
            return ::operator new(size);
         ...                         // 否则在这里处理
      }
      void Base::operator delete(void* rawMemory, std::size_t size) throw() {
         if (rawMemory == 0) return;     // 检查 null 指针
         if (size != sizeof(Base)) {     // 如果大小错误，令标准的 operator delete 处理此申请
             ::operator delete(rawMemory);
             return;
         }
         现在，归还 rawMemory 所指内存;
         return;
      }

52.写了 placement new 也要写 placement delete。
   (1) 当你写一个 placement operator new，请确定也写出了对应的 placement operator delete。如果没有这样做，
你的程序可能会发生隐微而时断时续的内存泄漏。
   (2) 当你声明 placement new 和 placement delete，请确定不要无意识 (非故意) 地遮掩了它们的正常版本。
      class StandardNewDeleteFroms {
         public:
            // normal new/delete
            static void* operator new(std::size_t size) throw(std::bad_alloc) {
               return ::operator new(size);
            }
            static void operator delete(void* pMemory) throw() {
               return ::operator delete(pMemory);
            }
            // placement new/delete
            static void* operator new(std::size_t size, void* ptr) throw() {
               return ::operator new(size, ptr);
            }
            static void operator delete(void* pMemory, void* ptr) throw() {
               return ::operator delete(pMemory, ptr);
            }
            // nothrow new/delete
            static void* operator new(std::size_t size, const std::nothrow_t& nt) throw() {
               return ::operator new(size, nt);
            }
            static void operator delete(void* pMemory, const std::nothrow_t&) throw() {
               return ::operator delete(pMemory);
            }
      };
      class Widget: public StandardNewDeleteFroms {    // 继承标准形式
          public:
             using StandardNewDeleteFroms::operator new;    //让这些形式可见
             using StandardNewDeleteFroms::operator delete;
             // 添加一个自定义的 placement new 
             static void* operator new(std::size_t size, std::ostream& logStream) throw(std::bad_alloc);
             // 添加一个对应的 placement delete 
             static void operator delete(void* pMemory, std::ostream& logStream) throw();
             ...
      };
      
53.不要轻忽编译器的警告。
   (1) 严肃对待编译器发出的警告信息。努力在你的编译器的最高 (最严苛) 警告级别下争取 "无任何警告" 的荣誉。
   (2) 不要过度倚赖编译器的报警能力，因为不同的编译器对待事情的态度并不相同。一旦移植到另一个编译器上，
你原本倚赖的警告信息有可能消失。

54.让自己熟悉包括 TR1 (Technical Report 1) 在内的标准程序库。
   (1) C++ 标准程序库的主要机能由 STL、iostreams、locales 组成。并包含 C99 标准程序库。
   (2) TR1 添加了智能指针 (例如 tr1::shared_ptr)、一般化函数指针 (tr1::function)、hash-based 容器、
正则表达式 (regular expressions) 以及另外 10 个组件的支持。
   (3) TR1 自身只是一份规范。为获得 TR1 提供的好处，你需要一份实物。一个好的实物来源是 Boost。
   
55.让自己熟悉 Boost。
   (1) Boost 是一个社群，也是一个网站。致力于免费、源码开放、同僚复审的 C++ 程序库开发。Boost 在 C++
标准化过程中扮演深具影响力的角色。
   (2) Boost 提供许多 TR1 组件实现品，以及其他许多程序库。
    字符串与文本处理、容器、函数对象和高级编程、泛型编程 (Generic programming)、
模版元编程 (Template metaprogramming，TMP)、数学和数值 (Math and numerics)、
正确性与测试 (Correctness and testing)、数据结构、语言间的支持 (Inter-language support)、
内存、杂项。
```
