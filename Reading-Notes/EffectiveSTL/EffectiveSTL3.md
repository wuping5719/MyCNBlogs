<h2>《Effective STL》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
21.总是让比较函数在等值情况下返回 false。
   struct StringPtrGreator:public binary_function<const string*, const string*, bool> {
       bool operator()(const string *ps1, const string *ps2) const {
          return *ps1 > *ps2;
       }
   };

22.切勿直接修改 set 或 multiset 中的键。
   
23.考虑用排序的 vector 替代关联容器。
```
