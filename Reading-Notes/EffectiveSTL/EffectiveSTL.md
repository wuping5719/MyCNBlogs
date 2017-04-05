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
```
