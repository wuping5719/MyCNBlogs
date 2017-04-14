<h2>《Effective STL》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
41.理解 ptr_fun、mem_fun 和 mem_fun_ref 的来由。
   for_each 算法：
     template<typename InputIterator, typename Function>
     Function for_each(InputIterator begin, InputIterator end, Function f) {
        while (begin != end) f(*begin++);
     }
     vector<Widget> vw;     
     for_each(vw.begin(), vw.end(), test);        // 调用 #1
     // 该 mem_fun 声明针对不带参数的非 const 成员函数；C 是类，R 是所指向的成员函数返回类型
     template<typename R, typename C>  
     mem_fun_t<R, C>
     mem_fun(R(C::*pmf)());
     list<Widget *> lpw;
     ...
     for_each(lpw.begin(), lpw.end(), mem_fun(&Widget::test));   // 现在可以通过编译了
     for_each(vw.begin(), vw.end(), ptr_fun(test));    // 与调用 #1 的行为一样

42.确保 less<T> 与 operator< 具有相同的语义。
   Boost 库的智能指针 shared_ptr 的部分实现：
    namespace std {          // 针对 boost::shared_ptr<T> 的 std::less 特化
       template<typename T>
       struct less<boost::shared_ptr<T>>: public binary_function<boost::shared_ptr<T>, 
                     boost::shared_ptr<T>, bool> {
            bool operator() (const boost::shared_ptr<T>& a, const boost::shared_ptr<T>& b) const {
                return less<T*>()(a.get(), b.get());  // shared_ptr::get 返回 shared_ptr 对象的内置指针
            }    
       };
    }

43.算法调用优先于手写的循环。
   (1) 效率：算法通常比程序员自己写的循环效率更高。
   (2) 正确性：自己写循环比使用算法更容易出错。
   (3) 可维护性：使用算法的代码通常比手写循环的代码更简洁明了。
     deque<double> d;
     ...
     deque<double>::iterator insertLocation = d.begin();
     for (size_t i = 0; i < numDoubles; ++i) {
        // 每次 insert 调用之后都更新 insertLocation 以便保持迭代器有效
        insertLocation = d.insert(insertLocation, data[i] + 41);   
        ++insertLocation;
     }

44.容器的成员函数优先于同名的算法。

45.正确区分 count、find、binary_search、lower_bound、upper_bound 和 equal_range。
   判断一个 list 容器中是否存在某个特定的 Widget 对象值。
     list<Widget> lw;    // list 容器
     Widget w;           // 特定的 Widget 值
     if (find(lw.begin(), lw.end(), w) != lw.end()) {
        ...
     } else {
        ...
     }
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_CPlusPlusSet.png" />

```c++
46.考虑使用函数对象而不是函数作为 STL 算法的参数。
   (1) 将一个集合中每个字符串对象的长度输出到 cout 中：
      struct StringSize: public unary_function<string, string::size_type> {
         string::size_type operator()(const string& s) const {
            return s.size();
         }
      };
      transform(s.begin(), s.end(), ostream_iterator<string::size_type>(cout, "\n"), StringSize());
   (2) 一个函数模版的实例化名称并不完全等同于一个函数的名称：
      template<typename FPType>
      struct Average: public binary_function<FPType, FPType, FPType> {
         FPType operator() (FPType val1, FPType val2) const {
            return average(val1, val2);
         }
      };
      template<typename InputIter1, typename InputIter2>
      void writeAverages(InputIter1 begin1, InputIter1 end1, InputIter2 begin2, ostream& s) {
         transform(begin1, end1, begin2, 
               ostream_iterator<typename iterator_traits<InputIter1>::value_type>(s, "\n"), 
               Average<typename iterator_traits<InputIter1>::value_type>());
      }

47.避免产生 “直写型” (write-only) 的代码。
   有一个 vector<int>，删除其中所有值小于 x 的元素，但在最后一个其值不小于 y 的元素之前的所有元素都应该保留下来。
    vector<int> v;
    int x, y;
    ...
    typedef vector<int>::iterator VecIntIter;
    // 初始化 rangeBegin，使它指向 v 中大于等于 y 的最后一个元素之后的元素
    // 如果不存在这样的元素，则 rangeBegin 被初始化为 v.begin()
    // 如果最后这样的元素正好是 v 的最后一个元素，则 rangeBegin 被初始化为 v.end()
    VecIntIter rangeBegin = find_if(v.rbegin(), v.rend(), bind2nd(greater_equal<int>(), y)).base();
    // 在从 rangeBegin 到 v.end() 的区间中，删除所有小于 x 的值
    v.erase(remove_if(rangeBegin, v.end(), bind2nd(less<int>(), x)), v.end());

48.总是包含 (#include) 正确的头文件。
   (1) 几乎所有的标准 STL 容器都被声明在与之同名的头文件中，比如 vector 被声明在 <vector> 中，list 被声明
在 <list> 中，等等。但是 <set> 和 <map> 是个例外，<set> 中声明了 set 和 multiset，
<map> 中声明了 map 和 multimap。
   (2) 除了 4 个 STL 算法以外，其他所有的算法都被声明在 <algorithm> 中，这 4 个算法是 accumulate、
inner_product、adjacent_difference 和 partial_sum，它们被声明在头文件 <numeric> 中。
   (3) 特殊类型的迭代器，包括 istream_iterator 和 istreambuf_iterator，被声明在 <iterator> 中。
   (4) 标准的函数子 (比如 less<T>) 和函数子配接器 (比如 not1、bind2nd) 被声明在头文件 <functional> 中。
   
49.学会分析与 STL 相关的编译器诊断信息。

50.熟悉与 STL 相关的 Web 站点。
  (1) SGI STL 站点：http://www.sgi.com/tech/stl
  (2) STLport 站点：http://www.stlport.org
  (3) Boost 站点：http://www.boost.org
```
