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
