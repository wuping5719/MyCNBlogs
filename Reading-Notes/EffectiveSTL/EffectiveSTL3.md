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
   把排序的 vector 当做映射表来使用，其本质上就如同将它用做一个集合一样。
    vector<Data> vd;                    // 取代 map<string, int>
    ...                                 // 设置阶段：大量的插入，查找却很少
    sort(vd.begin(), vd.end());         // 设置阶段结束(当模拟 multiset 时，你可能选用 stable_sort)
    string s;                           // 用于存放待查找值的对象
    ...                                 // 开始查找阶段
    if (binary_search(vd.begin(), vd.end(), s, DataCompare()))  ...   // 通过 binary_search 查找
    vector<Data>::iterator i = lower_bound(vd.begin(), vd.end(), s, DataCompare()); // 通过 lower_bound 查找
    if (i != vd.end() && !DataCompare()(s, *i))  ...
    pair<vector<Data>::iterator, vector<Data>::iterator> range 
                          = equal_range(vd.begin(), vd.end(), s, DataCompare());   // 通过 equal_range 查找
    if (range.first != range.second()) ...
    ...                                 // 结束查找阶段，开始重组阶段
    sort(vd.begin(), vd.end(), DataCompare());   // 开始新查找阶段...

24.当效率至关重要时，请在 map::operator[] 与 map::insert 之间谨慎做出选择。
   如果要更新一个已有的映射表元素，则应该优先选择 operator[]；如果要添加一个新的元素，最好选择 insert。
      map<int, Widget> m;
      m[1] = 1.50;
   在功能上等同于：
      typedef map<int, Widget> IntWidgetMap;
      // 用键值 1 和默认构造的值对象创建一个新的 map 条目
      pair<IntWidgetMap::iterator, bool> result = m.insert(IntWidgetMap::value_type(1, Widget())); 
      result.first->second = 1.50;     // 为新构造的值对象赋值
   把对 operator[] 的使用换成对 insert 的直接调用：
      m.insert(IntWidgetMap::value_type(1, 1.50)); 

25.熟悉非标准的散列容器。
   (1) SGI 使用传统的开放式散列策略，由指向元素的单向链表的指针数组(桶)构成。
   SGI 的 hash_set 的声明：
      template<typename T, typename HashFunction = hash<T>, typename CompareFunction = equal_to<T>,
          typename Allocator = allocator<T>>
      class hash_set;
   (2) Dinkumware 基于一种新型数据结构，即由指向元素的双向链表迭代器数组组成，
相邻的一对迭代器标识了每个桶中元素的范围。
   Dinkumware 的 hash_set 的声明：
      template<typename T, typename CompareFunction>
      class hash_compare;
      template<typename T, typename HashingInfo = hash_compare<T, less<T>>, 
          typename Allocator = allocator<T>>
      class hash_set;

26.iterator 优先于 const_iterator、reverse_iterator 及 const_reverse_iterator。
   vector<T> 容器中 insert 和 erase 函数的原型：
      iterator insert(iterator position, const T& x);
      iterator erase(iterator position);
      iterator erase(iterator rangeBegin, iterator rangeEnd);

27.使用 distance 和 advance 将容器的 const_iterator 转换成 iterator。
      typedef deque<int> IntDeque;       // 类型定义，简化代码
      typedef IntDeque::iterator Iter;
      typedef IntDeque::const_iterator ConstIter;
      IntDeque d;
      ConstIter ci;
      ...                    // 使 ci 指向 d
      Iter i (d.begin());    // 使 i 指向 d 的起始位置
      // 将 i 和 ci 都当做 const_iterator，计算它们之间的距离，然后将 i 移动这段距离
      advance(i, distance<ConstIter>(i, ci));   

28.正确理解由 reverse_iterator 的 base() 成员函数所产生的 iterator 的用法。
   (1) 如果要在一个 reverse_iterator ri 指定的位置上插入新元素，则只需在 ri.base() 处插入元素即可。
对于插入操作而言，ri 和 ri.base() 是等价的。
   (2) 如果要在一个 reverse_iterator ri 指定的位置上删除一个元素，则需要在 ri.base() 前面的位置执行删除操作。
对于删除操作而言，ri 和 ri.base() 是不等价的。
      vector<int> v;
      ...
      vector<int>::reverse_iterator ri = find(v.rbegin(), v.rend(), 3);
      v.erase((++ri).base());

29.对于逐个字符的输入请考虑使用 istreambuf_iterator。
   把一个文本文件复制到一个 string 对象中：
      ifstream inputFile("interestData.txt");
      string fileData((istreambuf_iterator<char>(inputFile)), istreambuf_iterator<char>());

30.确保目标区间足够大。
```
