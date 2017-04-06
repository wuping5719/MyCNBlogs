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
```
