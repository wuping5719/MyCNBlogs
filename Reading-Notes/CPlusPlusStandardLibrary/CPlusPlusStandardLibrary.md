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
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_STL.png" />

```c++
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
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_algorithm2.png" />

```c++
```
