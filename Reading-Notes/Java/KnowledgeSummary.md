<h2>[Java 知识点总结] :memo: </h2> 

https://blog.csdn.net/sinat_34979383/article/details/60874347

> 一、Java 基础(语言、集合框架、OOP、设计模式等)
```
1.HashMap 和 Hashtable 的区别:
  Hashtable 是基于陈旧的 Dictionary 的 Map 接口的实现，而 HashMap 是基于哈希表的 Map 接口的实现。
从方法上看，HashMap 去掉了 Hashtable 的 contains 方法。
  HashTable 是同步的(线程安全)，而 HashMap 线程不安全，效率上 HashMap 更快。
  HashMap 允许空键值，而 Hashtable 不允许。
  HashMap 的 iterator 迭代器执行快速失败机制，也就是说在迭代过程中修改集合结构，除非调用迭代器自身的 remove 方法，
否则以其他任何方式的修改都将抛出并发修改异常。而 Hashtable 返回的 Enumeration 不是快速失败的。
  注：Fast-fail 机制:在使用迭代器的过程中有其它线程修改了集合对象结构或元素数量,都将抛出 ConcurrentModifiedException，
但是抛出这个异常是不保证的，我们不能编写依赖于此异常的程序。

2.Java 工具包中线程安全的类： Vector、Stack、HashTable、ConcurrentHashMap、Properties。

3.Java 集合框架(常用)：
  Collection - List - ArrayList
  Collection - List - LinkedList
  Collection - List - Vector - Stack
  Collection - Set - HashSet
  Collection - Set - TreeSet
  Collection - Map - HashMap   
  Collection - Map - TreeMap
  Collection - Map - HashTable
  Collection - Map - LinkedHashMap
  Collection - Map - ConcurrentHashMap
  (1) List 集合和 Set集合: 
   List 中元素存取是有序的、可重复的；Set 集合中元素是无序的，不可重复的。
   CopyOnWriteArrayList: COW 的策略，即写时复制的策略。适用于读多写少的并发场景。
   Set 集合元素存取无序，且元素不可重复。
   HashSet 不保证迭代顺序，线程不安全；LinkedHashSet 是 Set 接口的哈希表和链接列表的实现，保证迭代顺序，线程不安全。
   TreeSet：可以对 Set 集合中的元素排序，元素以二叉树形式存放，线程不安全。
  (2) ArrayList、LinkedList、Vector 的区别：
   ArrayList、LinkedList 的区别：
   随机存取：ArrayList 是基于可变大小的数组实现，LinkedList 是链接列表的实现。
这也就决定了对于随机访问的 get 和 set 的操作，ArrayList 要优于 LinkedList，因为 LinkedList 要移动指针。
   插入和删除：LinkedList 要好一些，因为 ArrayList 要移动数据，更新索引。
   内存消耗：LinkedList 需要更多的内存，因为需要维护指向后继结点的指针。
   Vector 从 JDK 1.0 起就存在，在 1.2时 改为实现 List 接口，功能与 ArrayList 类似，但是 Vector具备线程安全。
  (3) Map 集合: 
   Hashtable: 基于 Dictionary 类，线程安全，速度快。底层是哈希表数据结构。是同步的。 不允许 null 作为键，null 作为值。
   Properties: Hashtable 的子类。用于配置文件的定义和操作，使用频率非常高，同时键和值都是字符串。
   HashMap：线程不安全，底层是数组加链表实现的哈希表。允许 null 作为键，null 作为值。HashMap 去掉了 contains 方法。 
注意：HashMap 不保证元素的迭代顺序。如果需要元素存取有序，请使用 LinkedHashMap。
   TreeMap：可以用来对 Map 集合中的键进行排序。
   ConcurrentHashMap: 是 JUC 包下的一个并发集合。
  (4) 为什么使用 ConcurrentHashMap 而不是 HashMap 或 Hashtable？
   HashMap 的缺点：主要是多线程同时 put 时，如果同时触发了 rehash 操作，会导致 HashMap 中的链表中出现循环节点，
进而使得后面 get 的时候，会死循环，CPU 达到 100%，所以在并发情况下不能使用 HashMap。让 HashMap 同步：
Map m = Collections.synchronizeMap(hashMap); 而 Hashtable 虽然是同步的，使用 synchronized 来保证线程安全，
但在线程竞争激烈的情况下 HashTable 的效率非常低下。因为当一个线程访问 HashTable 的同步方法，
其他线程再访问 HashTable 的同步方法时，可能会进入阻塞或轮询状态。如线程 1 使用 put 进行添加元素，
线程 2 不但不能使用 put 方法添加元素，并且也不能使用 get 方法来获取元素，所以竞争越激烈效率越低。

4.接口与抽象类的区别：
  (1) 一个子类只能继承一个抽象类, 但能实现多个接口。
  (2) 抽象类可以有构造方法, 接口没有构造方法。
  (3) 抽象类可以有普通成员变量, 接口没有普通成员变量。
  (4) 抽象类和接口都可有静态成员变量, 抽象类中静态成员变量访问类型任意，接口只能 public static final(默认)。
  (5) 抽象类可以没有抽象方法, 抽象类可以有普通方法, 接口中都是抽象方法。
  (6) 抽象类可以有静态方法，接口不能有静态方法。
  (7) 抽象类中的方法可以是 public、protected; 接口方法只有 public abstract。

5.创建线程有几种不同的方式？你喜欢哪一种？为什么？
  (1) 继承 Thread 类;
  (2) 实现 Runnable 接口;
  (3) 应用程序可以使用 Executor 框架来创建线程池;
  (4) 实现 Callable 接口。
  我更喜欢实现 Runnable 接口这种方法，当然这也是现在大多程序员会选用的方法。
  因为一个类只能继承一个父类而可以实现多个接口。同时，线程池也是非常高效的，很容易实现和使用。
```

> 二、Java 高级(JavaEE、框架、服务器、工具等)

> 三、多线程和并发

> 四、Java 虚拟机

> 五、数据库(Sql、MySQL、Redis 等)

> 六、算法与数据结构

> 七、计算机网络

> 八、操作系统(OS 基础、Linux 等)

> 九、其他
