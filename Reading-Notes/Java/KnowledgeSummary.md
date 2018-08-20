<h2>[Java 知识点总结] :memo: </h2> 

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

5.final 关键字。
  final 修饰的变量是常量，必须进行初始化，可以显示初始化，也可以通过构造进行初始化，如果不初始化编译会报错。

6.异常。
  相关的关键字 throw、throws、try...catch, finally。
  throws：用在方法签名上, 以便抛出的异常可以被调用者处理。
  throw：方法内部通过 throw 抛出异常。
  try...catch, finally：用于检测包住的语句块, 若有异常, catch 子句捕获并执行 catch 块。

7.关于 finally。
  (1) finally 不管有没有异常都要处理。
  (2) 当 try 和 catch 中有 return 时，finally 仍然会执行，finally 比 return 先执行。
  (3) 不管有没有异常抛出, finally 在 return 返回前执行。
  (4) finally 是在 return 后面的表达式运算后执行的(此时并没有返回运算后的值，而是先把要返回的值保存起来，
不管 finally 中的代码怎么样，返回的值都不会改变，仍然是之前保存的值)，所以函数返回值是在 finally 执行前确定的。
  注意：finally 中最好不要包含 return，否则程序会提前退出，返回值不是 try 或 catch 中保存的返回值。
finally 不执行的几种情况：程序提前终止如调用了 System.exit, 病毒，断电。

8.this & super.
  1) super 出现在父类的子类中。有三种存在方式：
   (1) super.xxx(xxx 为变量名或对象名); 意思是获取父类中 xxx 的变量或引用。
   (2) super.xxx(); (xxx为方法名)意思是直接访问并调用父类中的方法。
   (3) super(); 调用父类构造。
  注：super 只能指代其直接父类。
  2) this() & super() 在构造方法中的区别：
   (1) 调用 super() 必须写在子类构造方法的第一行, 否则编译不通过。
   (2) super 从子类调用父类构造, this 在同一类中调用其他构造。
   (3) 均需要放在第一行。
   (4) 尽管可以用 this 调用一个构造器, 却不能调用 2 个。
   (5) this 和 super 不能出现在同一个构造器中, 否则编译不通过。
   (6) this()、super() 都指的对象, 不可以在 static 环境中使用。
   (7) 本质 this 指向本对象的指针。super 是一个关键字。

9.面向对象的五大基本原则(SOLID)。
  (1) S 单一职责(SRP): Single-Responsibility Principle 一个类, 最好只做一件事, 只有一个引起它的变化。
单一职责原则可以看做是低耦合, 高内聚在面向对象原则的引申, 将职责定义为引起变化的原因, 以提高内聚性减少引起变化的原因。
  (2) O 开放封闭原则(OCP): Open-Closed Principle 软件实体应该是可扩展的, 而不是可修改的。
对扩展开放,对修改封闭。
  (3) L 里氏替换原则(LSP): Liskov-Substitution Principle 子类必须能够替换其基类。
这一思想表现为对继承机制的约束规范, 只有子类能够替换其基类时, 才能够保证系统在运行期内识别子类, 这是保证继承复用的基础。
  (4) I 接口隔离原则(ISP): Interface-Segregation Principle 使用多个小的接口, 而不是一个大的总接口。
  (5) D 依赖倒置原则(DIP): Dependency-Inversion Principle 依赖于抽象。
具体而言就是高层模块不依赖于底层模块, 二者共同依赖于抽象。抽象不依赖于具体, 具体依赖于抽象。

10.null 可以被强制转型为任意类型的对象。

11.代码执行次序。
   (1) 多个静态成员变量, 静态代码块按顺序执行。
   (2) 单个类中: 静态代码 -> main 方法 -> 构造块 -> 构造方法。
   (3) 构造块在每一次创建对象时执行。
   (4) 涉及父类和子类的初始化过程：
     a.初始化父类中的静态成员变量和静态代码块；
     b.初始化子类中的静态成员变量和静态代码块；
     c.初始化父类的普通成员变量和构造代码块(按次序)，再执行父类的构造方法(注意父类构造方法中的子类方法覆盖)；
     d.初始化子类的普通成员变量和构造代码块(按次序)，再执行子类的构造方法。

12.数组复制方法。
   (1) for 逐一复制。
   (2) System.arraycopy() -> 效率最高 native 方法。
   (3) Arrays.copyOf() -> 本质调用 arraycopy。
   (4) clone 方法 -> 返回 Object[], 需要强制类型转换。

13.多态。
   (1) Java 通过方法重写和方法重载实现多态。
   (2) 方法重写是指子类重写了父类的同名方法。
   (3) 方法重载是指在同一个类中，方法的名字相同，但是参数列表不同。

14.形参 & 实参。
   (1) 形式参数可被视为 local variable。形参和局部变量一样都不能离开方法。只有在方法中使用，不会在方法外可见。
   (2) 形式参数只能用 final 修饰符，其它任何修饰符都会引起编译器错误。但是用这个修饰符也有一定的限制，
就是在方法中不能对参数做任何修改。不过一般情况下，一个方法的形参不用 final 修饰。只有在特殊情况下，那就是：方法内部类。
一个方法内的内部类如果使用了这个方法的参数或者局部变量的话，这个参数或局部变量应该是 final。
   (3) 形参的值在调用时根据调用者更改，实参则用自身的值更改形参的值(指针、引用皆在此列)，也就是说真正被传递的是实参。

15.Java 语言特性。
   (1) Java 致力于检查程序在编译和运行时的错误。
   (2) Java 虚拟机实现了跨平台接口。
   (3) 类型检查帮助检查出许多开发早期出现的错误。
   (4) Java 自己操纵内存减少了内存出错的可能性。
   (5) Java 还实现了真数组，避免了覆盖数据的可能。

16.Java 语法糖。
   (1) Java7 的 switch 用字符串 - hashcode 方法；switch 用于 enum 枚举。
   (2) 伪泛型 - List 原始类型。
   (3) 自动装箱拆箱 - Integer.valueOf 和 Integer.intValue。
   (4) foreach 遍历 - Iterator 迭代器实现。
   (5) 条件编译。
   (6) enum 枚举类、内部类。
   (7) 可变参数 - 数组。
   (8) 断言语言。
   (9) try 语句中定义和关闭资源。

17.Java 中 ++ 操作符是线程安全的吗？
   不是线程安全的操作。它涉及到多个指令，如读取变量值，增加，然后存储回内存，这个过程可能会出现多个线程交差。
还会存在竞态条件(读取-修改-写入)。

18.a = a + b 与 a += b 的区别。
   += 隐式的将加操作的结果类型强制转换为持有结果的类型。
如果两这个整型相加，如 byte、short 或者 int，首先会将它们提升到 int 类型，然后在执行加法操作。
   byte a = 127;
   byte b = 127;
   b = a + b;  // error : cannot convert from int to byte
   b += a;  // ok
因为 a + b 操作会将 a、b 提升为 int 类型，所以将 int 类型赋值给 byte 就会编译出错。

19.poll() 方法和 remove() 方法的区别？
   poll() 和 remove() 都是从队列中取出一个元素，但是 poll() 在获取元素失败的时候会返回空，
但是 remove() 失败的时候会抛出异常。

20.Java 中，Comparator 与 Comparable 有什么不同？
   Comparable 接口用于定义对象的自然顺序，而 comparator 通常用于定义用户定制的顺序。
   Comparable 总是只有一个，但是可以有多个 comparator 来定义对象的顺序。

21.final、finalize 和 finally 的不同之处？
   final 是一个修饰符，可以修饰变量、方法和类。如果 final 修饰变量，意味着该变量的值在初始化后不能被改变。
   Java 技术允许使用 finalize() 方法在垃圾收集器将对象从内存中清除出去之前做必要的清理工作。
这个方法是由垃圾收集器在确定这个对象没有被引用时对这个对象调用的，但是什么时候调用 finalize 没有保证。
   finally 是一个关键字，与 try 和 catch 一起用于异常的处理。finally 块一定会被执行，无论在 try 块中是否有发生异常。

22.说出几点 Java 中使用 Collections 的最佳实践。
   (1) 使用正确的集合类，例如: 如果不需要同步列表，使用 ArrayList 而不是 Vector。
   (2) 优先使用并发集合，而不是对集合进行同步。并发集合提供更好的可扩展性。
   (3) 使用接口代表和访问集合，如: 使用 List 存储 ArrayList，使用 Map 存储 HashMap 等等。
   (4) 使用迭代器来循环集合。
   (5) 使用集合的时候使用泛型。

23.Java 中，Serializable 与 Externalizable 的区别？
   Serializable 接口是一个序列化 Java 类的接口，以便于它们可以在网络上传输或者可以将它们的状态保存在磁盘上，
是 JVM 内嵌的默认序列化方式，成本高、脆弱而且不安全。Externalizable 允许你控制整个序列化过程，
指定特定的二进制格式，增加安全机制。

24.JDK 1.7 新特性。
  (1) switch 语句支持字符串变量：允许 switch 中有 String 变量和文本。
  (2) 泛型实例化类型自动推断：菱形操作符(<>)用于泛型推断，不再需要在变量声明的右边申明泛型。
  (3) 新的整数字面表达方式 - "0b"前缀和 "_" 连数符。
  (4) 在单个 catch 代码块中捕获多个异常，以及用升级版的类型检查重新抛出异常。
  (5) try-with-resources 语句：在使用流或者资源的时候，不需要手动关闭，Java 会自动关闭。
  (6) Fork-Join 池：某种程度上实现 Java 版的 Map-reduce。

25.JDK 1.8 新特性。
  (1) Lambda 表达式 − Lambda 允许把函数作为一个方法的参数(函数作为参数传递进方法中)。
  (2) 方法引用 − 方法引用提供了非常有用的语法，可以直接引用已有 Java 类或对象(实例)的方法或构造器。
与 lambda 联合使用，方法引用可以使语言的构造更紧凑简洁，减少冗余代码。
  (3) 默认方法 − 默认方法就是一个在接口里面有了一个实现的方法。
  (4) 新工具 − 新的编译工具，如：Nashorn 引擎 jjs、 类依赖分析器 jdeps。
  (5) Stream API − 新添加的 Stream API(java.util.stream) 把真正的函数式编程风格引入到 Java 中。
  (6) Date Time API − 加强对日期与时间的处理。
  (7) Optional 类 − Optional 类已经成为 Java 8 类库的一部分，用来解决空指针异常。

26.说出几条 Java 中方法重载的最佳实践？
  下面有几条可以遵循的方法重载的最佳实践来避免造成自动装箱的混乱。
  (1) 不要重载这样的方法：一个方法接收 int 参数，而另一个方法接收 Integer 参数。
  (2) 不要重载参数数量一致，而只是参数顺序不同的方法。
  (3) 如果重载的方法参数个数多于 5 个，采用可变参数。

27.String、StringBuffer 与 StringBuilder 的区别。
   (1) 可变和适用范围。String 对象是不可变的，而 StringBuffer 和 StringBuilder 是可变字符序列。
每次对 String 的操作相当于生成一个新的 String 对象，而对 StringBuffer 和 StringBuilder 的操作是对对象本身的操作，
而不会生成新的对象，所以对于频繁改变内容的字符串避免使用 String，因为频繁的生成对象将会对系统性能产生影响。
   (2) 线程安全。String 由于有 final 修饰，是 immutable 的，安全性是简单而纯粹的。
StringBuilder 和 StringBuffer 的区别在于 StringBuilder 不保证同步，也就是说如果需要线程安全需要使用 StringBuffer，
不需要同步的 StringBuilder 效率更高。

28.Comparable 和 Comparator 接口区别。
   Comparator 位于包 java.util 下，而 Comparable 位于包 java.lang 下。
   如果我们需要使用 Arrays 或 Collections 的排序方法对对象进行排序时，我们需要在自定义类中实现 Comparable 接口
并重写 compareTo 方法，compareTo 方法接收一个参数，如果 this 对象比传递的参数小，相等或大时分别返回负整数、0、正整数。
Comparable 被用来提供对象的自然排序。String、Integer 实现了该接口。
   Comparator 比较器的 compare 方法接收 2 个参数，根据参数的比较大小分别返回负整数、0 和正整数。 
Comparator 是一个外部的比较器，当这个对象自然排序不能满足你的要求时，你可以写一个比较器来完成两个对象之间大小的比较。
用 Comparator 是策略模式(strategy design pattern)，就是不改变对象自身，
而用一个策略对象(strategy object)来改变它的行为。

29.IO 和 NIO 简述。
   (1) 简述:
   在以前的 Java IO 中，都是阻塞式 IO，NIO 引入了非阻塞式 IO。
   第一种方式：我从硬盘读取数据，然后程序一直等，数据读完后，继续操作。这种方式是最简单的，叫阻塞 IO。
   第二种方式：我从硬盘读取数据，然后程序继续向下执行，等数据读取完后，通知当前程序(对硬件来说叫中断，对程序来说叫回调)，
 然后此程序可以立即处理数据，也可以执行完当前操作在读取数据。
   (2) 流与块的比较：
   原来的 I/O 以流的方式处理数据，而 NIO 以块的方式处理数据。面向流的 I/O 系统一次一个字节地处理数据。
一个输入流产生一个字节的数据，一个输出流消费一个字节的数据。这样做是相对简单的。不利的一面是，面向流的 I/O 通常相当慢。
一个面向块的 I/O 系统以块的形式处理数据。每一个操作都在一步中产生或者消费一个数据块。
按块处理数据比按(流式的)字节处理数据要快得多。但是面向块的 I/O 缺少一些面向流的 I/O 所具有的优雅性和简单性。
   (3) 通道与流：
   Channel 是一个对象，可以通过它读取和写入数据。通道与流功能类似，不同之处在于通道是双向的。
而流只是在一个方向上移动(一个流必须是 InputStream 或者 OutputStream 的子类)，而通道可以用于读、写或者同时用于读写。
   (4) 缓冲区 Buffer：
   在 NIO 库中，所有数据都是用缓冲区处理的。
   Position: 表示下一次访问的缓冲区位置。 
   Limit: 表示当前缓冲区存放的数据容量。 
   Capacity: 表示缓冲区最大容量。
   flip() 方法: 读写模式切换。
   clear() 方法: 它将 limit 设置为与 capacity 相同。它设置 position 为 0。
```

> 二、Java 高级(JavaEE、框架、服务器、工具等)
```
A.Spring Data JPA
1.Repository 接口：它是 Spring Data 的一个核心接口，它不提供任何方法，
开发者需要在自己定义的接口中声明需要的方法。
 public interface Repository<T, ID extends Serializable> {}
 (1) Repository：仅仅是一个标识，表明任何继承它的均为仓库接口类。
 (2) CrudRepository：继承 Repository，实现了一组 CRUD 相关的方法。 
 (3) PagingAndSortingRepository：继承 CrudRepository，实现了一组分页排序相关的方法。 
 (4) JpaRepository：继承 PagingAndSortingRepository，实现一组 JPA 规范相关的方法。 

2.JpaSpecificationExecutor 接口：不属于 Repository 体系，实现一组 JPA Criteria 查询相关的方法。
  (1) findOne(Specification<T>):T
  (2) findAll(Specification<T>):List<T>
  (3) findAll(Specification<T>, Pageable):Page<T>
  (4) findAll(Specification<T>, Sort):List<T>
  (5) count(Specification<T>):long
  Specification：封装 JPA Criteria 查询条件。
  通常使用匿名内部类的方式来创建该接口的对象。
  
3.Spring Data 方法定义规范。
  Keyword                  Sample                              JPQL snippet
And              findByLastNameAndFirstName            ... where x.lastname = ?1 and firstname = ?2
Or               findByLastNameOrFirstName             ... where x.lastname = ?1 or firstname = ?2
Is,Equals        findByFirstname,findByFirstnameEquals ... where x.firstname = ?1
Between          findByStartDateBetween                ... where x.startDate between ?1 and ?2
LessThan         findByAgeLessThan                     ... where x.age < ?1
LessThanEqual    findByAgeLessThanEqual                ... where x.age <= ?1
GreaterThan      findByAgeGreaterThan                  ... where x.age > ?1
GreaterThanEqual   findByAgeGreaterThanEqual           ... where x.age >= ?1
After              findByStartDateAfter                ... where x.startDate > ?1
Before             findByStartDateBefore               ... where x.startDate < ?1
IsNull             findByAgeIsNull                     ... where x.age is null
IsNotNull,NotNull  findByAge(Is)NotNull                ... where x.age not null
Like               findByFirstnameLike                 ... where x.firstname like ?1
NotLike          findByFirstnameNotLike                ... where x.firstname not like ?1
StartingWith     findByFirstnameStartingWith   ... where x.firstname like ?1(parameter bound with appended %)
EndingWith       findByFirstnameEndingWith     ... where x.firstname like ?1(parameter bound with prepended %)
Containing       findByFirstnameContaining     ... where x.firstname like ?1(parameter bound wrapped in %)
OrderBy          findByAgeOrderByLastnameDesc          ... where x.age = ?1 order by x.lastname desc
Not              findByLastnameNot                     ... where x.lastname <> ?1
In               findByAgeIn(Collection<Age> ages)     ... where x.age in ?1
NotIn            findByAgeNotIn(Collection<Age> age)   ... where x.age not in ?1
True             findByActiveTrue()                    ... where x.active = true
False            findByActiveFalse()                   ... where x.active = false
IgnoreCase       findByFirstnameIgnoreCase             ... where UPPER(x.firstame) = UPPER(?1)

4.流式查询结果。
  流在使用结束后需要关闭以释放资源，可以用 close() 方法手动将其关闭或者使用 try-with-resources 块。
  @Query("select u from User u")
  Stream<User> findAllByCustomQueryAndStream();

  try (Stream<User> stream = repository.findAllByCustomQueryAndStream()) {
      stream.forEach(…);
  }

5.异步查询结果。
  (1) 使用 java.util.concurrent.Future 作为返回类型。
   @Async
   Future<User> findByFirstname(String firstname);    
   
  (2) 使用 Java 8 java.util.concurrent.CompletableFuture 作为返回类型。
   @Async
   CompletableFuture<User> findOneByFirstname(String firstname);

  (3) 使用 org.springframework.util.concurrent.ListenableFuture 作为返回类型。
   @Async
   ListenableFuture<User> findOneByLastname(String lastname);

B.Servlet
1.Servlet 继承实现结构:
  Servlet(接口)            -->      init|service|destroy 方法
  GenericServlet(抽象类)   -->      与协议无关的 Servlet
  HttpServlet(抽象类)      -->      实现了 http 协议
  自定义 Servlet           -->      重写 doGet / doPost

2.编写 Servlet 的步骤:
  (1) 继承 HttpServlet。
  (2) 重写 doGet / doPost 方法。
  (3) 在 web.xml 中注册 servlet。

3.Servlet 生命周期：
  (1) init: 仅执行一次, 负责装载 Servlet 时初始化 Servlet 对象。
  (2) service: 核心方法, 一般 get/post 两种方式。
  (3) destroy: 停止并卸载 Servlet, 释放资源。

4.处理过程：
  (1) 客户端 request 请求 -> 服务器检查 Servlet 实例是否存在 -> 若存在调用相应 service 方法。
  (2) 客户端 request 请求 -> 服务器检查 Servlet 实例是否存在 -> 若不存在装载 Servlet 类并创建实例 
  -> 调用 init 初始化 -> 调用 service。
  (3) 加载和实例化、初始化、处理请求、服务结束。

5.doPost 方法要抛出的异常: ServletExcception、IOException。

6.Servlet 容器装载 Servlet:
  (1) web.xml 中配置 load-on-startup 启动时装载。
  (2) 客户首次向 Servlet 发送请求。
  (3) Servlet 类文件被更新后, 重新装载 Servlet。

7.HttpServlet 容器响应 web 客户请求流程:
  (1) Web 客户向 Servlet 容器发出 http 请求。
  (2) Servlet 容器解析 Web 客户的 http 请求。
  (3) Servlet 容器创建一个 HttpRequest 对象, 封装 http 请求信息。
  (4) Servlet 容器创建一个 HttpResponse 对象。
  (5) Servlet 容器调用 HttpServlet 的 service 方法, 把 HttpRequest 和 HttpResponse 对象作为 
service 方法的参数传给 HttpServlet 对象。
  (6) HttpServlet 调用 HttpRequest 的有关方法, 获取 http 请求信息。
  (7) HttpServlet 调用 HttpResponse 的有关方法, 生成响应数据。
  (8) Servlet 容器把 HttpServlet 的响应结果传给 Web 客户。

8.HttpServletRequest 完成的一些功能：
  (1) request.getCookie();
  (2) request.getHeader(String s);
  (3) request.getContextPath();
  (4) request.getSession().

9.HttpServletResponse 完成一些的功能：
  (1) 设 http 响应头;
  (2) 设置 Cookie;
  (3) 输出返回数据.

10.JSP 九大内置对象与 Servlet 的关系：
   Out          -->      response.getWriter
   Request      -->      Service 方法中的 req 参数
   Response     -->      Service 方法中的 resp 参数
   Session      -->      request.getSession
   Application  -->      getServletContext
   Exception    -->      Throwable
   Page         -->      this
   PageContext  -->      PageContext
   Config       -->      getServletConfig
   Exception是 JSP 九大内置对象之一，其实例代表其他页面的异常和错误。
只有当页面是错误处理页面时，即 isErroePage 为 true 时，该对象才可以使用。

C.JSP：JSP 的前身就是 Servlet。

D.Tomcat
  1.Tomcat 容器的等级：Container - Engine - Host - Servlet - 多个Context(一个Context对应一个Web工程) - Wrapper

E.Hibernate
1.Hibernate 的 7 大鼓励措施：
  (1) 尽量使用 many-to-one, 避免使用单项 one-to-many。
  (2) 灵活使用单项 one-to-many。
  (3) 不用一对一, 使用多对一代替一对一。
  (4) 配置对象缓存, 不使用集合对象。
  (5) 一对多使用 bag, 多对一使用 set。
  (6) 继承使用显示多态。
  (7) 消除大表, 使用二级缓存。
  
2.Hibernate 延迟加载：
  (1) Hibernate2 延迟加载实现：a) 实体对象 b) 集合(Collection)。
  (2) Hibernate3 提供了属性的延迟加载功能：当 Hibernate 在查询数据的时候，数据并没有存在与内存中，
当程序真正对数据的操作时，对象才存在与内存中，就实现了延迟加载，他节省了服务器的内存开销，从而提高了服务器的性能。
  (3) Hibernate 使用 Java 反射机制，而不是字节码增强程序来实现透明性。
  (4) Hibernate 的性能非常好，因为它是个轻量级框架。映射的灵活性很出色。
它支持各种关系数据库，从一对一到多对多的各种复杂关系。

F.XML 与 JSON 对比：
  1) XML
  (1) 应用广泛，可扩展性强，被广泛应用各种场合。
  (2) 读取、解析没有 JSON 快。
  (3) 可读性强，可描述复杂结构。
  2) JSON
  (1) 结构简单，都是键值对。
  (2) 读取、解析速度快，很多语言支持。
  (3) 传输数据量小，传输速率大大提高。
  (4) 描述复杂结构能力较弱。
  JavaScript、PHP 等原生支持，简化了读取解析。成为当前互联网时代普遍应用的数据结构。
```

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
<p><img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_20180805.jpg" /></p>

```
6.在监视器(Monitor)内部是如何做线程同步的？程序应该做哪种级别的同步？
  在 Java 虚拟机中, 每个对象(Object 和 class)通过某种逻辑关联监视器, 每个监视器和一个对象引用相关联, 
为了实现监视器的互斥功能, 每个对象都关联着一把锁。一旦方法或者代码块被 synchronized 修饰, 
那么这个部分就放入了监视器的监视区域, 确保一次只能有一个线程执行该部分的代码, 线程在获取锁之前不允许执行该部分的代码。
另外 Java 还提供了显式监视器 (Lock) 和隐式监视器 (synchronized) 两种锁方案。

7.什么是死锁(deadlock)？
  死锁: 是指两个或两个以上的进程在执行过程中, 因争夺资源而造成的一种互相等待的现象, 若无外力作用, 它们都将无法推进下去。
  产生原因：
   (1) 因为系统资源不足。
   (2) 进程运行推进顺序不合适。
   (3) 资源分配不当。
   (4) 占用资源的程序崩溃。
  如果系统资源充足，进程的资源请求都能够得到满足，死锁出现的可能性就很低，否则就会因争夺有限的资源而陷入死锁。
其次，进程运行推进顺序与速度不同，也可能产生死锁。
  下面四个条件是死锁的必要条件，只要系统发生死锁，这些条件必然成立，而只要下列条件之一不满足，就不会发生死锁。
    A.互斥条件：一个资源每次只能被一个进程使用。
    B.请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
    C.不剥夺条件: 进程已获得的资源，在末使用完之前，不能强行剥夺。
    D.循环等待条件: 若干进程之间形成一种头尾相接的循环等待资源关系。
  死锁的解除与预防：
    理解了死锁的原因，尤其是产生死锁的四个必要条件，就可以最大可能地避免、预防和解除死锁。所以，在系统设计、
进程调度等方面注意如何不让这四个必要条件成立，如何确定资源的合理分配算法，避免进程永久占据系统资源。
此外，也要防止进程在处于等待状态的情况下占用资源。因此，对资源的分配要给予合理的规划。

8.volatile。
  Java 语言提供了一种稍弱的同步机制, 即 volatile 变量。用来确保将变量的更新操作通知到其他线程, 
保证了新值能立即同步到主内存, 以及每次使用前立即从主内存刷新。当把变量声明为 volatile 类型后, 
编译器与运行时都会注意到这个变量是共享的。volatile 修饰变量, 每次被线程访问时强迫其从主内存重读该值, 修改后再写回。
保证读取的可见性, 对其他线程立即可见。volatile 的另一个语义是禁止指令重排序优化。
但是 volatile 并不保证原子性, 也就不能保证线程安全。
  (1) volatile 禁止指令重排序的底层原理：指令重排序，是指 CPU 允许多条指令不按程序规定的顺序分开发送给相应电路单元处理。
但并不是说任意重排，CPU 需要能正确处理指令依赖情况以正确的执行结果。volatile 禁止指令重排序是通过内存屏障实现的，
指令重排序不能把后面的指令重排序到内存屏障之前。由内存屏障保证一致性。注：该条语义在 JDK1.5 才得以修复，
这点也是 JDK1.5 之前无法通过双重检查加锁来实现单例模式的原因。
  (2) volatile 类型变量提供什么保证？
  volatile 变量提供有序性和可见性保证，例如：JVM 或者 JIT 为了获得更好的性能会对语句重排序，
但是 volatile 类型变量即使在没有同步块的情况下赋值也不会与其他语句重排序。 volatile 提供 happens-before 的保证，
确保一个线程的修改能对其他线程是可见的。某些情况下，volatile 还能提供原子性，如读 64 位数据类型，
像 long 和 double 都不是原子的，但 volatile 类型的 double 和 long 就是原子的。
  volatile 的使用场景：
   A.运算结果不依赖变量的当前值，或者能够确保只有单一的线程修改该值。
   B.变量不需要与其他状态变量共同参与不变约束。
  (3) 你是如何调用 wait() 方法的？使用 if 块还是循环？为什么？
  wait() 方法应该在循环调用，因为当线程获取到 CPU 开始执行的时候，其他条件可能还没有满足，所以在处理前，
循环检测条件是否满足会更好。下面是一段标准的使用 wait 和 notify 方法的代码：
   // The standard idiom for using the wait method
   synchronized (obj) {
      while (condition does not hold)
         obj.wait(); // (Releases lock, and reacquires on wakeup)
         ... // Perform action appropriate to condition
   }

9.ReadWriteLock(读写锁): 写写互斥；读写互斥；读读并发。在读多写少的情况下可以提高效率。

10.resume(继续挂起的线程)和 suspend(挂起线程)一起用。

11.wait 与 notify、notifyall 一起用。

12.sleep 与 wait 的异同点。
   (1) sleep 是 Thread 类的静态方法, wait 来自 Object 类。
   (2) sleep 方法短暂停顿不释放锁, wait 方法条件等待要释放锁，因为只有这样，其他等待的线程才能在满足条件时获取到该锁。
   (3) wait, notify, notifyall 必须在同步代码块中使用, sleep 可以在任何地方使用。
   (4) 都可以抛出 InterruptedException。

13.让一个线程停止执行：异常 - 停止执行；休眠 - 停止执行；阻塞 - 停止执行。

14.ThreadLocal。
   (1) ThreadLocal 解决了变量并发访问的冲突问题。
   当使用 ThreadLocal 维护变量时, ThreadLocal 为每个使用该变量的线程提供独立的变量副本, 
每个线程都可以独立地改变自己的副本, 而不会影响其它线程所对应的副本, 是线程隔离的。
线程隔离的秘密在于 ThreadLocalMap 类(ThreadLocal 的静态内部类)。
   (2) 与 synchronized 同步机制的比较：
   首先, 它们都是为了解决多线程中相同变量访问冲突问题。不过, 在同步机制中, 
要通过对象的锁机制保证同一时间只有一个线程访问该变量。该变量是线程共享的, 
使用同步机制要求程序缜密地分析什么时候对该变量读写, 什么时候需要锁定某个对象, 什么时候释放对象锁等复杂的问题, 
程序设计编写难度较大, 是一种“以时间换空间”的方式。而 ThreadLocal 采用了以“以空间换时间”的方式。
   (3) 线程局部变量原理：
   当使用 ThreadLocal 维护变量时, ThreadLocal 为每个使用该变量的线程提供独立的变量副本, 
 每个线程都可以独立地改变自己的副本, 而不会影响其它线程所对应的副本, 是线程隔离的。
 线程隔离的秘密在于 ThreadLocalMap 类(ThreadLocal 的静态内部类)。
   线程局部变量是局限于线程内部的变量，属于线程自身所有，不在多个线程间共享。Java 提供 ThreadLocal 类来支持线程局部变量，
是一种实现线程安全的方式。但是在管理环境下(如 Web 服务器)使用线程局部变量的时候要特别小心，在这种情况下，
工作线程的生命周期比任何应用变量的生命周期都要长。任何线程局部变量一旦在工作完成后没有释放，Java 应用就存在内存泄露的风险。
   ThreadLocal 的方法：void set(T value)、T get() 以及 T initialValue()。
   ThreadLocal 是如何为每个线程创建变量的副本的：
   首先，在每个线程 Thread 内部有一个 ThreadLocal.ThreadLocalMap 类型的成员变量 threadLocals，
这个 threadLocals 就是用来存储实际的变量副本的，键值为当前 ThreadLocal 变量，value 为变量副本(即 T 类型的变量)。
初始时，在 Thread 里面，threadLocals 为空，当通过 ThreadLocal 变量调用 get() 方法或者 set() 方法，
就会对 Thread 类中的 threadLocals 进行初始化，并且以当前 ThreadLocal 变量为键值，
以 ThreadLocal 要保存的副本变量为 value，存到 threadLocals。然后在当前线程里面，如果要使用副本变量，
就可以通过 get 方法在 threadLocals 里面查找。
   总结：
   A.实际的通过 ThreadLocal 创建的副本是存储在每个线程自己的 threadLocals 中的。
   B.为何 threadLocals 的类型 ThreadLocalMap 的键值为 ThreadLocal 对象，因为每个线程中可有多个 threadLocal 变量，
就像上面代码中的 longLocal 和 stringLocal；
   C.在进行 get 之前，必须先 set，否则会报空指针异常；如果想在 get 之前不需要调用 set 就能正常访问的话，
必须重写 initialValue() 方法。

15.JDK 提供的用于并发编程的同步器。
   (1) Semaphore：Java 并发库的 Semaphore 可以很轻松完成信号量控制，Semaphore 可以控制某个资源可被同时访问的个数，
通过 acquire() 获取一个许可，如果没有就等待，而 release() 释放一个许可。
   (2) CyclicBarrier：主要的方法就是一个：await()。await()方法每被调用一次，计数便会减少 1，并阻塞住当前线程。
当计数减至 0 时，阻塞解除，所有在此 CyclicBarrier 上面阻塞的线程开始运行。
   (3) CountDownLatch：直译过来就是倒计数(CountDown)门闩(Latch)。倒计数不用说，门闩的意思顾名思义就是阻止前进。
在这里就是指 CountDownLatch.await() 方法在倒计数为 0 之前会阻塞当前线程。

16.什么是 Busy spin？我们为什么要使用它？
   Busy spin 是一种在不释放 CPU 的基础上等待事件的技术。它经常用于避免丢失 CPU 缓存中的数据
(如果线程先暂停，之后在其他 CPU 上运行就会丢失)。所以，如果你的工作要求低延迟，并且你的线程目前没有任何顺序，
这样你就可以通过循环检测队列中的新消息来代替调用 sleep() 或 wait() 方法。它唯一的好处就是你只需等待很短的时间，
如几微秒或几纳秒。LMAX 分布式框架是一个高性能线程间通信的库，该库有一个 BusySpinWaitStrategy 类就是基于这个概念实现的，
使用 busy spin 循环 EventProcessors 等待屏障。

17.Java 中，编写多线程程序的时候你会遵循哪些最佳实践？
  (1) 给线程命名，这样可以帮助调试。
  (2) 最小化同步的范围，而不是将整个方法同步，只对关键部分做同步。
  (3) 如果可以，更偏向于使用 volatile 而不是 synchronized。
  (4) 使用更高层次的并发工具，而不是使用 wait() 和 notify() 来实现线程间通信，
如 BlockingQueue，CountDownLatch 及 Semeaphore。
  (5) 优先使用并发集合，而不是对集合进行同步。并发集合提供更好的可扩展性。

18.Happens-Before 规则：
  (1) 程序次序规则：按控制流顺序先后发生。
  (2) 管程锁定规则：一个 unlock 操作先行发生于后面对同一个锁的 lock 操作。
  (3) volatile 变量规则：对一个 volatile 变量的写操作先行发生于后面对这个变量的读操作。
  (4) 线程启动规则：start 方法先行发生于线程的每一个动作。
  (5) 线程中断规则：对线程的 interrupt 方法调用先行发生于被中断线程的代码检测到中断时间的发生。
  (6) 线程终止规则：线程内的所有操作都先行发生于对此线程的终止检测。
  (7) 对象终结规则：一个对象的初始化完成先行发生于它的 finalize 方法的开始。
  (8) 传递性：如果 A 先行发生于操作 B，B 先行发生于操作 C，则 A 先行发生于操作 C。
```

> 四、Java 虚拟机
```
1.对哪些区域回收？
  Java运行时数据区域：程序计数器、JVM 栈、本地方法栈、方法区和堆。
  由于程序计数器、JVM 栈、本地方法栈 3 个区域随线程而生随线程而灭，对这几个区域内存的回收和分配具有确定性。
而方法区和堆则不一样，程序需要在运行时才知道创建哪些对象，对这部分内存的分配是动态的，GC 关注的也就是这部分内存。

2.怎样判断是否需要收集？
  (1) 引用计数法：对象没有任何引用与之关联(无法解决循环引用)。
   ext：Python 使用引用计数法。
  (2) 可达性分析法：通过一组称为 GC Root 的对象为起点, 从这些节点向下搜索，如果某对象不能从
这些根对象的一个(至少一个)所到达, 则判定该对象应当回收。
   ext：可作为 GCRoot 的对象：虚拟机栈中引用的对象。方法区中类静态属性引用的对象，方法区中类常量引用的对象，
本地方法栈中 JNI 引用的对象。

3.主动 GC：
  (1) 调用 System.gc(); 
  (2) Runtime.getRuntime.gc();
```

> 五、数据库(Sql、MySQL、Redis 等)

> 六、算法与数据结构

> 七、计算机网络

> 八、操作系统(OS 基础、Linux 等)

> 九、其他
