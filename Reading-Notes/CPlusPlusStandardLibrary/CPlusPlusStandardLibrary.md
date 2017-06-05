<h2>《C++ 标准库(The C++ Standard Library)》 :books: </h2> 

> [德] Nicolai M.Josuttis 著    电子工业出版社
 
```c++

************* 声明:本书 1099 页，要看完需要不少时间，适合作为手边的工具书 *************

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
       class tuple {
          public:
             explicit tuple(const Types&...);
             template <typename... UTypes> explicit tuple(UTypes&&...);
             ...
       };
    }
    
5.Smart Pointer (智能指针)。
  (1) Class shared_ptr 实现共享式拥有 (shared ownership) 概念。多个 smart pointer 可以指向相同对象，
该对象和其相关资源会在 “最后一个 reference 被销毁” 时被释放。标准库提供 weak_ptr、bad_weak_ptr 和
enable_shared_from_this 等辅助类。
  (2) Class unique_ptr 实现独占式拥有 (exclusive ownership) 或严格拥有 (strict ownership) 概念，
保证同一时间只有一个 smart pointer 可以指向该对象。你可以移交拥有权。它对于避免资源泄漏特别有用。

6.数值的极值 (Numeric Limit)。
  C++ 标准库借由 template numeric_limits 提供这些极值，用以取代传统 C 语言所采用的预处理器常量
(preprocessor constant)。你仍然可以使用后者，其中整数常量定义于 <climits> 和 <limits.h> ，
浮点常量定义于 <cfloat> 和 <float.h>。

7.Type Trait 和 Type Utility。
  (1) Type Trait 提供一种用来处理 type 属性的办法。它是个 template，是泛型代码的基石，可在编译器根据一或
多个 template 实参 (通常也是 type) 产出一个 type 或 value。
  (2) Reference Wrapper (外覆器)：声明于 <functional> 中的 class std::reference_wrapper<> 主要用来 “喂”
reference 给 function template，后者原本以 by value 方式接受参数。对于一个给定类型 T，这个 class 提供 ref()
用以隐式转换为 T&，一个 cref() 用以隐式转换为 const T&，这往往允许 function template 得以操作 reference 
而不需要另写特化版本。
  (3) Function Wrapper (外覆器)：class std::function_wrapper<>，声明于 <functional>，提供多态外覆器，
可以慨括 function pointer 记号。这个 class 允许你把可调用对象 (callable object，如 function、member_function、
function object 和 lambda) 当作最高级对象。

8.Clock 和 Timer。
  (1) Duration (时间段)：它是一个数值 (表现 tick 个数) 和一个分数 (表现时间单位，以秒计) 的组合。
其中的分数由 class ratio 描述。
   例：std::chrono::duration<long, std::ratio<1, 1000>> oneMillisecond(1);
  (2) Clock 定义出一个 epoch (起始点) 和一个 tick 周期。Clock 提供的函数 now() 可以产出一个代表 “现在时刻”
的 timepoint 对象。C++ 标准库提供三个 clock：system_clock、steady_clock、high_resolution_clock。
  (3) Timepoint (时间点) 表现出某个特定时间点，关联至某个 clock 的某个正值或负值 duration。
    namespace std {
       namespace chrono {
          template <typename Clock, typename Duration = typename Clock::duration>
          class time_point;
       }
    }
    
9.容器 (Container)。
  (1) 序列式容器 (Sequence Container)：一种有序集合，其内每个元素均有确凿的位置—取决于插入时机和地点，
与元素值无关。STL 提供了 5 个序列式容器：array、vector、deque、list 和 forward_list。
序列式容器通常被实现为 array 或 linked list。
  (2) 关联式容器 (Associative Container)：一种已排序集合，元素位置取决于其 value (或 key)
和给定的某个排序准则。STL 提供了 4 个关联式容器：set、multiset、map 和 multimap。
关联式容器通常被实现为 binary tree。
  (3) 无序容器 (Unordered Container)：一种无序集合，其内每个元素的位置无关紧要，唯一重要的是某特定元素
是否位于此集合内。STL 提供了 4 个无序容器：unordered_set、unordered_multiset、
unordered_map 和 unordered_multimap。无序容器通常被实现为 hash table。
  (4) 容器适配器 (Container Adapter)：Stack、Queue 和 Priority Queue。

10.错误处理 (Error Handling)。
   使用 STL，必须满足以下要求：
   (1) 迭代器务必合法而有效。使用前必须先初始化。
   (2) 迭代器如果指向 “逾尾” 位置，它并不指向任何对象，因此不能调用 operator * 或 operator ->。
   (3) 区间必须合法：用以 “指出某个区间” 的前后迭代器，必须指向同一个容器；从第一个迭代器出发，必须可以
到达第二个迭代器所指位置。
   (4) 如果涉及的区间不止一个，第二区间及后继各区间必须拥有 “至少和第一个区间一样多” 的元素。
   (5) 覆写动作中的 “标的区间” 必须拥有足够元素，否则就必须采用 Insert Iterator。
   
11.各种容器的使用时机。
   (1) 默认情况下应使用 vector。 vector 的内部结构最简单，并允许随机访问，所以数据的访问十分灵活，
数据的处理也够快。
   (2) 如果经常要在序列头部和尾部安插和移除元素，应该采用 deque。如果你希望元素被移除时，容器能够自动缩减
内部内存用量，那么也该采用 deque。由于 vector 通常采用一个内存区块来存放元素，而 deque 采用多个区块，
所以后者可内含更多元素。
   (3) 如果需要经常在容器中段执行元素的安插、移除和移动，可考虑使用 list。list 提供特殊的成员函数，
可以在常量时间内将元素从 A 容器转移到 B 容器。但由于 list 不支持随机访问，所以如果只知道 list 的头部
却要造访 list 的中段元素，效能会大打折扣。
   (4) 如果你要的容器对异常的处理使得 “每次操作若不成功便无任何作用”，那么应该选用 list (但是不调用其
assignment 操作符和 sort()；而且如果元素比较过程中会抛出异常，就不要调用 merge()、remove()、remove_if()、
和 unique() ) 或选用 associative/unordered 容器(但是不调用多元素安插动作，而且如果比较准则的复制/赋值动作
可能抛出异常，就不要调用 swap() 或 erase())。
   (5) 如果你经常需要根据某个准则查找元素，应当使用 “依据该准则进行 hash” 的 unordered set 或 multiset。
hash 容器内是无序的，如果你必须依赖元素的次序 (order)，应该使用 set 或 multiset，它们根据查找准则对元素
排序。
   (6) 如果想处理 key/value pair，请采用 unordered (multi)map。如果元素次序很重要，可采用 (multi)map。
   (7) 如果需要关联式数组，应采用 unordered map。如果元素次序很重要，可采用 map。
   (8) 如果需要字典结构，应采用 unordered multimap。如果元素次序很重要，可采用 multimap。

12.STL 迭代器。
   (1) 迭代器 (Iterator)：前向迭代器、双向迭代器、随机访问迭代器、输入型迭代器和输出型迭代器。
      for (auto pos = coll.begin(); pos != coll.end(); ++pos) { ... }
   (2) 迭代器适配器(Iterator Adapter)：Insert Iterator、Stream Iterator、Reverse Iterator、Move Iterator。
   (3) 迭代器相关的辅助函数：advance()、next()、prev()、distance() 和 iter_swap()。

13.Lambda。
   C++ 11 引入 lambda，允许 inline 函数的定义式被用作一个参数，或是一个 local 对象。
   Capture (用以访问外部作用域)：
     [=] 意味着外部作用域以 by value 方式传递给 lambda。因此当这个 lambda 被定义时，你可以读取所有可读数据，
但不能改动它们。
     [&] 意味着外部作用域以 by reference 方式传递给 lambda。因此当这个 lambda 被定义时，
你对所有数据的涂写动作都合法。
    #include <iostream>
    using namespace std;
    int main() {
       auto pow3 = [] (int i) {
           return i * i * i;
       };
       cout << "x*x*x: " << pow3(8) << endl;
    }
    
14.STL 算法。
  欲使用 C++ 标准库的算法，必须包含头文件 <algorithm>。
    #include <algorithm>
  用于数值处理的算法需要包含头文件 <numeric>。
    #include <numeric>
  STL 算法分类：非更易型算法 (nonmodifying algorithm)、更易型算法 (modifying algorithm)、移除型算法 (removing
algorithm)、变序型算法 (mutating algorithm)、排序算法 (sorting algorithm)、已排序区间算法 
(sorted-range algorithm)、数值算法 (numeric algorithm)。
```
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_algorithm2.png">非更易型算法 (nonmodifying algorithm)</a>

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_algorithm.png">更易型算法 (modifying algorithm)</a>

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_algorithm3.png">移除型算法 (removing algorithm)</a>

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_algorithm4.png">变序型算法 (mutating algorithm)</a>

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_algorithm5.png">排序算法 (sorting algorithm)</a>

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_algorithm6.png">已排序区间算法 (sorted-range algorithm)</a>

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_algorithm7.png">数值算法 (numeric algorithm)</a>

```c++
15.特殊容器。
  容器适配器：stack、queue 和 priority queue。
  class stack 的定义：
     namespace std {
        template <typename T, typename Container = deque<T>>
        class stack;
     }
  class queue 的定义：
     namespace std {
        template <typename T, typename Container = deque<T>>
        class queue;
     }
  class priority_queue 的定义：
     namespace std {
        template <typename T, typename Container = vector<T>, 
                  typename Compare = less<typename Container::value_type>>
        class priority_queue;
     }

16.字符串。
   在 <string> 中，class basic_string<> 被定义为所有 string 类型的基础模版类：
      namespace std {
          template <typename charT, typename traits = char_traits<charT>, 
                    typename Allocator = allocator<charT>>
          class basic_string;
      }
   string 是针对 char 而预定义的特化版本：
      namespace std {
          typedef basic_string<char> string;
      }
   对于使用宽字符 (例如 Unicode 或某些亚洲字符集) 的 string，存在三个预定义的特化版本 
(其中 u16string 和 u32string 始自 C++11):
      namespace std {
          typedef basic_string<wchar_t> wstring;
          typedef basic_string<char16_t> u16string;
          typedef basic_string<char32_t> u32string;
      }
 
```
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_strings.png">String 的各项操作</a>

```c++
17.正则表达式。
   (1) Match：将整个输入拿来比对 (匹配) 某个正则表达式。
   (2) Search：查找 “与正则表达式吻合” 的 pattern。
   (3) Tokenize：根据 “被指定为正则表达式” 的切分器 (separator) 取得语汇单元 (token)。
   (4) Replace：将与正则表达式吻合之第一个 (或后续所有) 子序列替换掉。
   
18.基本 Stream Class 及其层次体系。
   (1) 基类 ios_base 定义了 stream class 的所有 “与字符类型及其相应之 char trait 无关” 的属性，
主要包含状态和格式标志 (state and format flag) 等组件和函数。
   (2) 由 ios_base 派生的 class template basic_ios<>，定义出 “与字符类型及其相应之 char trait 相依赖”
的 stream class 共同属性，其中包括 stream 所用的缓冲器。缓冲器所属 class 派生自 
class template basic_streambuf<>，其实例化实参和 basic_ios<> 一致。basic_streambuf<> 负责实际的读/写操作。
   (3) class template basic_istream<> 和 basic_ostream<> 两者都以 virtual 方式继承自 basic_ios<>，
分别定义出用于读/写的对象。和 basic_ios<> 一样，它们以字符类型及其 trait 作为参数。如果无关乎国际化议题，
一般使用由字符类型 char 实例化出来的 istream 和 ostream 就够了。
   (4) class template basic_iostream<> 派生 (多重继承) 自 basic_istream<> 和 basic_ostream<>，
用来定义既可读亦可写的对象。
   (5) class template basic_streambuf<> 是 IOStream 程序库的核心，定义出所有 “可写的 stream” 或 
“可读的 stream” 的接口。其它 stream class 均利用它进行实际的字符读/写工作。

19.并发。
  (1) 高级接口：async() 和 Future。
   async() 提供一个接口，让一段机能或说一个 callable object 若是可能的话在后台运行，成为一个独立线程。
   class future<> 允许你等待线程结束并获取其结果 (一个返回值，或者一个异常)。
   如果你使用 async()，就应该以 by value 方式传递所有 “用来处理目标函数” 的必要 object，使 async() 只需
使用局部拷贝 (local copy)。
  (2) 低层接口：Thread 和 Promise。
  (3) 互斥体 (Mutex) 和 锁 (Lock)。
  (4) Condition Variable (条件变量)。
  (5) Atomic。
```

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_StandardExceptions.png"> 标准异常之层次体系 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_STL.png"> DNS 域名解析 </a>
