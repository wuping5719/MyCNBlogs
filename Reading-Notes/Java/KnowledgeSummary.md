<h2>[Java 研发工程师知识点总结] :memo: </h2> 
 
> 一、Java 基础(语言、集合框架、OOP、设计模式等)
```java
1.HashMap 和 Hashtable 的区别:
  Hashtable 是基于陈旧的 Dictionary 的 Map 接口的实现，而 HashMap 是基于哈希表的 Map 接口的实现。
从方法上看，HashMap 去掉了 Hashtable 的 contains 方法。
  HashTable 是同步的(线程安全)，而 HashMap 线程不安全，效率上 HashMap 更快。
  HashMap 允许空键值，而 Hashtable 不允许。
  HashMap 的 iterator 迭代器执行快速失败机制，也就是说在迭代过程中修改集合结构，除非调用迭代器自身的 remove 方法，
否则以其他任何方式的修改都将抛出并发修改异常。而 Hashtable 返回的 Enumeration 不是快速失败的。
  注：Fast-fail 机制:在使用迭代器的过程中有其它线程修改了集合对象结构或元素数量,都将抛出 ConcurrentModifiedException，
但是抛出这个异常是不保证的，我们不能编写依赖于此异常的程序。
```

> 二、Java 高级(JavaEE、框架、服务器、工具等)

> 三、多线程和并发

> 四、Java 虚拟机

> 五、数据库(Sql、MySQL、Redis 等)

> 六、算法与数据结构

> 七、计算机网络

> 八、操作系统(OS 基础、Linux 等)

> 九、其他
