<h2>《Effective STL》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
1.慎重选择容器类型。
  (1) 标准 STL 序列容器：vector、string、deque 和 list。
  (2) 标准 STL 关联容器：set、multiset、map 和 multimap。
  (3) 非标准序列容器：slist 和 rope。(slist：单向链表，rope：本质上是一个 “重型” string)
  (4) 非标准的关联容器：hash_set、hash_multiset、hash_map、hash_multimap。
  (5) vector<char> 作为 string 的替代。
  (6) vector 作为标准关联容器的替代。
  (7) 几种标准的非 STL 容器：数组、bitset、valarray、stack、queue 和 priority_queue。

2.不要试图编写独立于容器类型的代码。
  使用封装 (encapsulation) 技术从一种容器类型转到另一种。
  (1) class Widget { ... };
      typedef vector<Widget> WidgetContainer;
      WidgetContainer wc;
      Widget bestWidget;
      ...
      WidgetContainer::iterator i = find(wc.begin(), wc.end(), bestWidget);
  (2) class Widget { ... };
      template<typename T>
      SpecialAllocator { ... };
      typedef vector<Widget, SpecialAllocator<Widget>> WidgetContainer;
      WidgetContainer wc;
      Widget bestWidget;
      ...
      WidgetContainer::iterator i = find(wc.begin(), wc.end(), bestWidget);

3.确保容器中的对象拷贝正确而高效。

4.调用 empty 而不是检查 size() 是否为 0。

5.区间成员函数优先于与之对应的单元素成员函数。
  给定 v1 和 v2 两个向量(vector)，使 v1 的内容和 v2 的后半部分相同的最简单操作?
    v1.assign(v2.begin() + v2.size() / 2, v2.end());

6.当心 C++ 编译器最烦人的分析机制。
  一个存有整数 (int) 的文件，把这些整数复制到一个 list 中：
    ifstream dataFile("ints.dat");
    istream_iterator<int> dataBegin(dataFile);
    istream_iterator<int> dataEnd;
    list<int> data(dataBegin, dataEnd);

7.如果容器中包含了通过 new 操作创建的指针，切记在容器对象析构前将指针 delete 掉。
    void doSomething() {
       typedef boost::shared_ptr<Widget> SPW;
       vector<SPW> vwp;
       for(int i=0; i<SOME_MAGIC_NUMBER; ++i) 
          vwp.push_back(SPW(new Widget));    // 从 Widget* 创建 SPW，然后对它进行一次 push_back
       ...
    }
    
8.切勿创建包含 auto_ptr 的容器。
    template<class RandomAccessIterator, class Compare>
    void sort(RandomAccessIterator first, RandomAccessIterator last, Compare comp) {
       typedef typename iterator_traits<RandomAccessIterator>::value_type ElementType;
       RandomAccessIterator i;
       ...                          // 使 i 指向基准元素
       ElementType pivotValue(*i);  // 把基准元素复制到局部临时变量中
       ...                          // 做其余的排序工作  
    }

9.慎重选择删除元素的方法。
 (1) 要删除容器中有特定值的所有对象：
   如果容器是 vector、string 或 deque，则使用 erase-remove 习惯用法。
    Container<int> c;
    c.erase(remove(c.begin(), c.end(), 1963), c.end());
   如果容器是 list，则使用 list::remove。
    c.remove(1963);
   如果容器是一个标准关联容器，则使用它的 erase 成员函数。
    c.erase(1963);
 (2) 要删除容器中满足特定判别式 (条件) 的所有对象：
   如果容器是 vector、string 或 deque，则使用 erase-remove_if 习惯用法。
    bool badValue(int );     // 返回 x 是否为 "坏值"
    c.erase(remove_if(c.begin(), c.end(), badValue), c.end());
   如果容器是 list，则使用 list::remove_if。
    c.remove_if(badValue);
   如果容器是一个标准关联容器，则使用 remove_copy_if 和 swap，或者写一个循环来遍历容器中的元素，
记住当把迭代器传给 erase 时，要对它进行后缀递增。
  ① AssocContainer<int> c;             // 标准关联容器
    ...
    AssocContainer<int> goodValues;    // 保存不被删除的值的临时容器
    // 把不被删除的值从 c 复制到 goodValues 中
    remove_copy_if(c.begin(), c.end(), inserter(goodValues, goodValues.end()), badValue); 
    c.swap(goodValues);                // 交换 c 和 goodValues 的内容
  ② AssocContainer<int> c;
    ...
    // for 循环的第三部分是空的，i 在下面递增
    for (AssocContainer<int>::iterator i = c.begin(); i != c.end(); /*什么也不做*/ ) {
        if (badValue(*i))  c.erase(i++);    // 对坏值，把当前的 i 传给 erase，递增 i 是副作用
        else ++i;                           // 对好值，则简单地递增 i
    }
  (3) 要在循环内部做某些 (除了删除对象之外的) 操作：
   如果容器是一个标准序列容器，则写一个循环来遍历容器中的元素，记住每次调用 erase 时，要用它的返回值更新迭代器。
   如果容器是一个标准关联容器，则写一个循环来遍历容器中的元素，记住当把迭代器传给 erase 时，要对迭代器做后缀递增。

10.了解分配子 (allocator) 的约定和限制。
  (1) 你的分配子是一个模版，模版参数 T 代表你为它分配内存的对象的类型。
  (2) 提供类型定义 pointer 和 reference，但是始终让 pointer 为 T*，reference 为 T&。
  (3) 千万别让你的分配子拥有随对象而不同的状态。通常，分配子不应该有非静态的数据成员。
  (4) 记住，传给分配子的 allocate 成员函数的是那些要求内存的对象的个数，而不是所需的字节数。
这些函数返回 T* 指针，即使尚未有 T 对象被构造出来。
  (5) 一定要提供嵌套的 rebind 模版，因为标准容器依赖该模版。
    template <typename T>      // 标准的分配子这样声明
    class allocator {
       public:
          template<typename U>
          struct rebind {
             typedef allocator<U> other;
          };
          ...
    };
```
