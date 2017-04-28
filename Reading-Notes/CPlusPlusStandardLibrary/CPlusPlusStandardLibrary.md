<h2>《C++ 标准库(The C++ Standard Library)》 :books: </h2> 

> [德] Nicolai M.Josuttis 著    电子工业出版社
 
```c++
1.命名空间 (Namespace) std。
  (1) 以下 namespace 嵌套于 std 内，被 C++ 标准库使用：
   std::rel_ops, std::chrono, std::placeholders, std::regex_constants, std::this_thread.
  (2) 使用 C++ 标准库标识符的三种选择：
    ① 直接指定标识符。例：std::cout << std::hex << 3.4 << std::endl;
    ② 使用 using declaration。例：using std::cout;
    ③ 使用 using directive。例：using namespace std;

2.头文件 (Header File)。
   #include <string>      // C++ class string
   #include <cstring>     // char* functions from C  

3.差错和异常 (Error and Exception) 的处理。
  异常类的头文件：
   #include <exception>       // for classes exception and bad_exception
   #include <stdexcept>       // for most logic and runtime error classes
   #include <system_error>    // for system errors (since C++11)
   #include <new>             // for out-of-memory exceptions
   #include <ios>             // for I/O exceptions
   #include <future>          // for errors with async() and futures (since C++11)
   #include <typeinfo>        // for bad_cast and bad_typeid
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_StandardExceptions.png" />

```c++
4.Pair 和 Tuple。
  (1) Struct pair 定义于 <utility>，可以对 pair<> 执行 create、copy/assign/swap 及 compare 操作。
它提供 first_type 和 second_type 类型定义式，用来表示第一 value 和第二 value 的类型。
   namespace std {
      template <typename T1, typename T2>
      struct pair {
         T1 first;
         T2 second;
         pair(const T1& x, const T2& y);
         template <typename U, typename V> pair(U&& x, V&& y);
         template <typename... Args1, typename... Args2> pair(piecewise_construct_t, 
                                          tuple<Args1...> first_args, tuple<Args2...> second_args);
         ...
      };
   }
  (2) Tuple (不定数的值组)：它扩展了 pair 的概念，拥有任意数量的元素。
    namespace std {
       template <typename... Types>
       class tuple;
    }
```
