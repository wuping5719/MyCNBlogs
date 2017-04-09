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
   EmpIDSet se;
   Employee selectedID;                           // selectedID 是存储有特定 ID 号的 Employee 变量
   ...
   EmpIDSet::iterator i = se.find(selectedID);    // 第1步：找到待修改的元素
   if (i != se.end()) {
       Employee e(*i);                            // 第2步：拷贝该元素
       e.setTitle("Corporate Deity");             // 第3步：修改拷贝
       se.erase(i++);                             // 第4步：删除该元素；递增迭代器以保持它的有效性
       se.insert(i, e);                           // 第5步：插入新元素，它的位置和原来的相同
   }

23.考虑用排序的 vector 替代关联容器。
```
