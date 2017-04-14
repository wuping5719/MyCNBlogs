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
```
```c++
45.正确区分 count、find、binary_search、lower_bound、upper_bound 和 equal_range。
```
<table>
  <tr style="rowspan=2">
    <td>想知道什么</td>
    <td>使用算法</td>
    <td>使用成员函数</td>
  </tr>
  <tr>
    <td>对未排序的区间</td>
    <td>对排序的区间</td>
    <td>对 set 或 map</td>
    <td>对 multiset 或 multimap</td>
  </tr>
</table>

```c++
46.考虑使用函数对象而不是函数作为 STL 算法的参数。

47.避免产生 “直写型” (write-only) 的代码。

48.总是包含 (#include) 正确的头文件。

49.学会分析与 STL 相关的编译器诊断信息。

50.熟悉与 STL 相关的 Web 站点。
```
