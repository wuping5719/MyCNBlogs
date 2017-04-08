<h2>《Effective STL》 :books: </h2> 

> Scott Meyers 著    电子工业出版社

```c++
11.理解自定义分配子的合理用法。
   假定你有一些特殊过程，它们采用 malloc 和 free 内存模型来管理一个位于共享内存的堆：
     void* mallocShared(size_t bytesNeeded);
     void* freeShared(void* ptr);
而你想把 STL 容器的内容放到这块共享内存中去。
     template <typename T>
     class SharedMemoryAllocator {
        public:
           ...
           pointer allocate<size_type numObjects, const void *localityHint = 0) {
              return static_cast<pointer>(mallocShared(numObjects * sizeof(T)));
           }
           void deallocate(pointer ptrToMemory, size_type numObjects) {
              freeShared(ptrToMemory);
           }
           ...
     };
     typedef vector<double, SharedMemoryAllocator<double>> SharedDoubleVec;
     ... 
     {  
        ... // 开始某个代码块
        SharedDoubleVec v;   //创建一个 vector，其元素位于共享内存中
        ...
     }
     void *pVectorMemory = mallocShared(sizeof(SharedDoubleVec)); // 为 SharedDoubleVec 对象分配足够的内存
     // 使用 "placement new" 在内存中创建一个 SharedDoubleVec 对象
     SharedDoubleVec *pv = new (pVectorMemory) SharedDoubleVec;  
     ...  // 使用对象 (通过 pv)
     pv->~SharedDoubleVec();       // 析构共享内存中的对象
     freeShared(pVectorMemory);    // 释放最初分配的那一块共享内存

12.切勿对 STL 容器的线程安全性有不切实际的依赖。
   在一个 vector<int> 中查找值为 5 的第一个元素，如果找到了，就把该元素置为 0。
     template<typename Container>    // 一个为容器获取和释放互斥体的模版
     class Lock {     // 框架
        public:
           Lock(const Container& container) : c(container) {
              getMutexFor(c);       // 在构造函数中获取互斥体
           }
           ~Lock() {
              releaseMutexFor(c);   // 在析构函数中释放它
           }
        private:
           const Container& c;
     };
     vector<int> V;
     ...
     {                               // 创建新的代码块
        Lock<vector<int>> lock(v);   // 获取互斥体
        <vector<int>::iterator first5(find(v.begin(), v.end(), 5));
        if (first5 != v.end()) {
           *first5 = 0;
        }
     }                                // 代码块结束，自动释放互斥体
     
13.vector 和 string 优先于动态分配的数组。

14.使用 reserve 来避免不必要的重新分配。
   (1) size()：该容器有多少个元素。
   (2) capacity()：该容器利用已经分配的内存可以容纳多少个元素。
   (3) resize(Container::size_type n)：强迫容器改变到包含 n 个元素的状态。
   (4) reserve(Container::size_type n)：强迫容器把它的容量变为至少是 n，前提是 n 不小于当前的大小。
     vector<int> v;
     v.reserve(1000);
     for(int i = 1; i <= 1000; ++i)  v.push_back(i);

15.注意 string 实现的多样性。
  (1) 每个 string 实现都包含如下信息：
     ① 字符串的大小 (size)，即它包含的字符个数。
     ② 用于存储字符串的内存的容量 (capacity)。
     ③ 字符串的值 (value)，即构成该字符串的字符。
   还可能包含：
     ④ 它的分配子的一份拷贝。
   建立在引用计数基础上的 string 实现可能还包含：
     ⑤ 对值的引用计数。
  (2) string 不同实现的区别：
     ① string 的值可能会被引用计数，也可能不会。
     ② string 对象大小范围可以是一个 char* 指针的大小的 1 倍到 7 倍。
     ③ 创建一个新的字符串值可能需要零次、一次或两次动态分配内存。
     ④ string 对象可能共享，也可能不共享其大小和容量信息。
     ⑤ string 可能支持，也可能不支持针对单个对象的分配子。
     ⑥ 不同的实现对字符内存的最小分配单位有不同的策略。
     
16.了解如何把 vector 和 string 数据传给旧的 API。
  (1) c_str 函数返回一个指向字符串的值的指针，该指针可用于 C。
  可以把一个字符串 s 传给函数：void doSomething(const char* pString);
  如下所示：doSomething(s.c_str());
  (2) 用来自 C API 中的元素初始化一个 vector，可以利用 vector 和数组的内存布局兼容性，
向 API 传入该向量中元素的存储区域：
    // C API: 该函数以一个指向最多有 arraySize 个 double 类型数据的数组的指针为参数，并向该数组中写入数据。
    // 它返回已被写入的 double 数据的个数，这个数不会超过 arraySize。
    size_t fillArray(double* pArray, size_t arraySize);
    vector<double> vd(maxNumDoubles);      // 创建大小为 maxNumDoubles 的 vector
    // 使用 fillArray 向 vd 中写入数据然后把 vd 的大小改为 fillArray 所写入的元素的个数
    vd.resize(fillArray(&vd[0], vd.size())); 
    deque<double> d(vd.begin(), vd.end());  // 把数据复制到 deque 中
    list<double> l(vd.begin(), vd.end());   // 把数据复制到 list 中
    set<double> s(vd.begin(), vd.end());    // 把数据复制到 set 中
  (3) 用来自 C API 中的数据初始化一个 string，只要让 API 把数据放到一个 vector<char> 中，然后把数据从该
向量复制到相应字符串中：
    // C API: 该函数以一个指向最多有 arraySize 个 double 类型数据的数组的指针为参数，并向该数组中写入数据。
    // 它返回已被写入的 char 数据的个数，这个数不会超过 arraySize。
    size_t fillString(char* pArray, size_t arraySize);
    vector<char> vc(maxNumChars);      // 创建大小为 maxNumChars 的 vector
    size_t charsWritten = fillString(&vc[0], vc.size(0));  // 使用 fillString 向 vc 中写入数据
    string s(vc.begin(), vc.begin() + charsWritten);  // 通过区间构造函数，把数据从 vc 复制到 s 中

17.使用 "swap技巧" 除去多余的容量。
   shrink-to-fit (压缩至适当大小):
    class Contestant { ... };
    vector<Contestant> v;
    string s;
    ...                        // 使用 v 和 s
    // vector<Contestant>(v).swap(v);  // 从 v 中去除多余的容量
    vector<Contestant>().swap(v);      // 清除 v 并把它的容量变为最小
    string().swap(s);                  // 清除 s 并把它的容量变为最小

18.避免使用 vector<bool>。
    template<typename Allocator>
    vector<bool, Allocator> {
       public:
          class reference { ... };     // 用来为指向单个位的引用而产生代理的类
          reference operator[](size_type n); // operator[] 返回一个代理
          ...      
    };

19.理解相等 (equality) 和等价 (equivalence) 的区别。
   每个标准关联容器都通过 key_comp 成员函数使排序判别式可被外部使用，所以，如果下面的表达式为 true，则按照
关联容器 c 的排序准则，两个对象 x 和 y 有等价的值：
   // 在 c 的排列顺序中，x 在 y 之前不为真，y 在 x 之前也不为真
   !c.key_comp()(x, y) && !c.key_comp()(y, x)  

20.为包含指针的关联容器指定比较类型。
    struct StringPtrLess:public binary_function<const string*, const string*, bool> {
       bool operator()(const string *ps1, const string *ps2) const {
          return *ps1 < *ps2;
       }
    };
    typedef set<string*, StringPtrLess> StringPtrSet;   // 用 StringPtrLess 作为 ssp 的比较类型
    StringPtrSet ssp;  // 创建一个包含字符串的集合，并把它用 StringPtrLess 定义的顺序排序
    ssp.insert(new string("Anteater"));
    ...   // 插入4个字符串
    for(StringPtrSet::const_iterator i = ssp.begin()); i != ssp.end(); ++i)
       count << **i << endl;
```
