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
```

> 二、Java 高级(JavaEE、框架、服务器、工具等)

> 三、多线程和并发
```
1.创建线程有几种不同的方式？你喜欢哪一种？为什么？
  (1) 继承 Thread 类;
  (2) 实现 Runnable 接口;
  (3) 应用程序可以使用 Executor 框架来创建线程池;
  (4) 实现 Callable 接口。
  我更喜欢实现 Runnable 接口这种方法，当然这也是现在大多程序员会选用的方法。
  因为一个类只能继承一个父类而可以实现多个接口。同时，线程池也是非常高效的，很容易实现和使用。
  
2.简述线程，程序、进程的基本概念。以及它们之间关系是什么？
  (1) 线程与进程相似，但线程是一个比进程更小的执行单位。一个进程在其执行的过程中可以产生多个线程。
与进程不同的是同类的多个线程共享同一块内存空间和一组系统资源，所以系统在产生一个线程，或是在各个线程之间作切换工作时，
负担要比进程小得多，也正因为如此，线程也被称为轻量级进程。
  (2) 程序是含有指令和数据的文件，被存储在磁盘或其他的数据存储设备中，也就是说程序是静态的代码。
  (3) 进程是程序的一次执行过程，是系统运行程序的基本单位，因此进程是动态的。系统运行一个程序即是一个进程从创建，
运行到消亡的过程。简单来说，一个进程就是一个执行中的程序，它在计算机中一个指令接着一个指令地执行着，
同时，每个进程还占有某些系统资源如 CPU 时间，内存空间，文件，输入输出设备的使用权等等。换句话说，当程序在执行时，
将会被操作系统载入内存中。线程是进程划分成的更小的运行单位。线程和进程最大的不同在于：
基本上各进程是独立的，而各线程则不一定，因为同一进程中的线程极有可能会相互影响。从另一角度来说，进程属于操作系统的范畴，
主要是同一段时间内，可以同时执行一个以上的程序，而线程则是在同一程序内几乎同时执行一个以上的程序段。

3.什么是多线程？为什么程序的多线程功能是必要的？
  多线程就是几乎同时执行多个线程(一个处理器在某一个时间点上永远都只能是一个线程！即使这个处理器是多核的，
 除非有多个处理器才能实现多个线程同时运行)。几乎同时是因为实际上多线程程序中的多个线程实际上是一个线程执行一会
 然后其他的线程再执行，并不是很多书籍所谓的同时执行。这样可以带来以下的好处：
  (1) 使用线程可以把占据长时间的程序中的任务放到后台去处理;
  (2) 用户界面可以更加吸引人，比如用户点击了一个按钮去触发某些事件的处理，可以弹出一个进度条来显示处理的进度;
  (3) 程序的运行速度可能加快;
  (4) 在一些等待的任务实现上如用户输入、文件读写和网络收发数据等，线程就比较有用了。
在这种情况下可以释放一些珍贵的资源如内存占用等等。

4.多线程与多任务的差异是什么？
  多任务与多线程是两个不同的概念。
  多任务是针对操作系统而言的，表示操作系统可以同时运行多个应用程序。
  而多线程是针对一个进程而言的，表示在一个进程内部可以几乎同时执行多个线程。

5.线程有哪些基本状态？这些状态是如何定义的?
  (1) 新建(new)：新创建了一个线程对象。
  (2) 可运行(runnable)：线程对象创建后，其他线程(比如 main 线程)调用了该对象的 start() 方法。
该状态的线程位于可运行线程池中，等待被线程调度选中，获取 cpu 的使用权。
  (3) 运行(running)：可运行状态 (runnable) 的线程获得了 cpu 时间片(timeslice)，执行程序代码。
  (4) 阻塞(block)：阻塞状态是指线程因为某种原因放弃了 cpu 使用权，也即让出了 cpu timeslice，暂时停止运行。
直到线程进入可运行 (runnable) 状态，才有机会再次获得 cpu timeslice 转到运行 (running) 状态。阻塞的情况分三种：
   A.等待阻塞：运行 (running) 的线程执行 o.wait() 方法，JVM 会把该线程放入等待队列 (waitting queue) 中。
   B.同步阻塞：运行 (running) 的线程在获取对象的同步锁时，若该同步锁被别的线程占用，
则 JVM 会把该线程放入锁池 (lock pool) 中。
   C.其他阻塞: 运行 (running) 的线程执行 Thread.sleep(long ms) 或 t.join() 方法，或者发出了 I/O 请求时，
JVM 会把该线程置为阻塞状态。当 sleep() 状态超时 join() 等待线程终止或者超时、或者 I/O 处理完毕时，
线程重新转入可运行 (runnable) 状态。
  (5) 死亡(dead)：线程 run()、main() 方法执行结束，或者因异常退出了 run() 方法，则该线程结束生命周期。
死亡的线程不可再次复生。
```
<p><img src="https://user-gold-cdn.xitu.io/2017/12/15/16059cc91ee8efb3?w=876&h=492&f=png&s=-1" /></p>

> 四、Java 虚拟机

> 五、数据库(Sql、MySQL、Redis 等)

> 六、算法与数据结构

> 七、计算机网络

> 八、操作系统(OS 基础、Linux 等)

> 九、其他
