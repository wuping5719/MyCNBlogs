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

43.算法调用优先于手写的循环。

44.容器的成员函数优先于同名的算法。

45.正确区分 count、find、binary_search、lower_bound、upper_bound 和 equal_range。

46.考虑使用函数对象而不是函数作为 STL 算法的参数。

47.避免产生 “直写型” (write-only) 的代码。

48.总是包含 (#include) 正确的头文件。

49.学会分析与 STL 相关的编译器诊断信息。

50.熟悉与 STL 相关的 Web 站点。
```
