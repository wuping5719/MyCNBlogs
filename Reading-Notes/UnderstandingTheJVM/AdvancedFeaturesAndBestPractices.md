<h2>《深入理解 Java 虚拟机 —— JVM 高级特性与最佳实践》 :books: </h2> 

> 周志明 著    机械工业出版社

```java
1.Java 虚拟机发展史：
  (1) Sun Classic / Exact VM;
  (2) Sun HotSpot VM;
  (3) Sun Mobile_Embedded VM / Meta-Circular VM;
  (4) BEA JRockit / IBM J9 VM;
  (5) Azul VM / BEA Liquid VM;
  (6) Apache Harmony / Google Android Dalvik VM;
  (7) Microsoft JVM。

2.Java技术的未来：模块化、混合语言、多核并行、进一步丰富语法、64位虚拟机。

3.Java 运行时数据区：
  (1) 程序计数器 (Program Counter Register)：当前线程所执行的字节码的行号指示器。
  (2) 虚拟机栈 (VM Stack)： 线程私有，生命周期与线程相同。为虚拟机执行 Java 方法 (字节码) 服务。
  (3) 本地方法栈 (Native Method Stack)：为虚拟机使用到的 Native 方法服务。
  (4) Java 堆 (Heap)：Java 虚拟机所管理的内存中最大的一块。
  (5) 方法区 (Method Area)：与 Java 堆一样，是各个线程共享的内存区域，用于存储已被虚拟机加载的类信息、常量、
静态变量、即时编译器编译后的代码等数据。运行时常量池 (Runtime Constant Pool) 是方法区的一部分，用于存放编译期
生成的各种字面量和符号引用。
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_vm.png" />

```java
4.判断对象是否存活?
  (1) 引用计数算法 (Reference Counting);
  (2) 可达性分析算法 (Reachability Analysis)。

5.在 Java 语言中，可作为 GC Roots 的对象有：
  (1) 虚拟机栈 (栈帧中的本地变量表) 中引用的对象。
  (2) 方法区中静态属性引用的对象。
  (3) 方法区中常量引用的对象。
  (4) 本地方法栈中 JNI (即一般说的 Native 方法) 引用的对象。

6.Java 引用：JDK 1.2 之后，将引用分为强引用 (Strong Reference)、软引用 (Soft Reference)、
弱引用 (Weak Reference)、虚引用 (Phantom Reference) 4 种，这 4 种引用强度依次逐渐减弱。
  (1) 强引用是指在程序代码中普遍存在的，类似"Object obj = new Object()"这类的引用，只要强引用还存在，
垃圾收集器永远不会回收掉被引用的对象。
  (2) 软引用是用来描述一些还有用但并非必需的对象。对于软引用关联着的对象，在系统将要发生内存溢出异常之前，
将会把这些对象列进回收范围之中进行第二次回收。如果这次回收还没有足够的内存，才会抛出内存溢出异常。在JDK 1.2 之后，
提供 SoftReference 类来实现软引用。
  (3) 弱引用也用来描述非必需对象，但它的强度比软引用更弱一些，被弱引用关联的对象只能生存到下一次垃圾收集发生之前。
当垃圾收集器工作时，无论当前内存是否足够，都会回收掉只被弱引用关联的对象。在JDK 1.2 之后，
提供 WeakReference 类来实现弱引用。
  (4) 虚引用也称幽灵引用或幻影引用，它是最弱的一种引用关系。一个对象是否有虚引用的存在，完全不会对其生存时间
构成影响，也无法通过虚引用来取得一个对象实例。为一个对象设置虚引用关联的唯一目的就是能在这个对象被收集器回收时
收到一个系统通知。在JDK 1.2 之后，提供 PhantomReference 类来实现虚引用。

7.垃圾收集算法：
  (1) 标记-清除算法 (Mark-Sweep)。
  (2) 复制算法 (Copying)。
  (3) 标记-整理算法 (Mark-Compact)。
  (4) 分代收集算法 (Generational Collection)。

8.垃圾收集器：
  (1) Serial 收集器：虚拟机在 Client 模式下的默认新生代收集器。
  (2) ParNew 收集器：Serial 收集器的多线程版本，除了使用多条线程进行垃圾收集之外，其余行为包括 Serial 收集器
可用的所有控制参数 (如：-XX:SurvivorRatio、-XX:PretenureSizeThreshold、-XX:HandlePromotionFailure等)、
收集算法、Stop The World、对象分配规则、回收策略等都与 Serial 收集器完全一样。
运行在 Server 模式下的首选新生代收集器，目前只有它能与 CMS 收集器配合工作。
  (3) Parallel Scavenge 收集器：目标是达到一个可控制的吞吐量 (Throughput)。
      吞吐量 = 运行用户代码时间 / (运行用户代码时间 + 垃圾收集时间)
   用两个参数精确控制吞吐量，分别是控制最大垃圾收集停顿时间 (-XX:MaxGCPauseMillis) 参数和直接设置吞吐量大小 
(-XX:GCTimeRatio) 参数。
  (4) Serial Old 收集器：Serial 收集器的老年代版本，使用“标记-整理”算法。
  (5) Parallel Old 收集器：Parallel Scavenge 收集器的老年代版本，使用多线程和“标记-整理”算法。
  (6) CMS 收集器 (Concurrent Mark Sweep)：一种以获取最短回收停顿时间为目标的收集器，基于“标记-清除”算法实现。
包含 4 个运作步骤：初始标记(CMS initial mark)、并发标记(CMS concurrent mark)、重新标记(CMS remark)、
并发清除(CMS concurrent sweep)。
  (7) G1 收集器：一款面向服务端应用的垃圾收集器。G1 具备如下特点：并行与并发、分代收集、空间整合、可预测的停顿。
如果不计维护 Remembered Set 操作，G1 收集器运作大致分为以下几个步骤：初始标记(Initial Marking)、
并发标记(Concurrent Marking)、最终标记(Final Marking)、筛选回收(Live Data Counting and Evacuation)。
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_GC.png" />

```java
9.Minor GC 和 Full GC：
  (1) 新生代 GC (Minor GC)：指发生在新生代的垃圾收集动作，因为 Java 对象大多都具备朝生夕灭的特性，所以 MinorGC
非常频繁，一般回收速度也比较快。
  (2) 老年代 GC (Major GC / Full GC)：指发生在老年代的 GC，出现了 Major GC，经常会伴随至少一次的 Minor GC
(但非绝对，在 Parallel Scavenge 收集器的收集策略里就有直接进行 Major GC 的策略选择过程)。Major GC 的速度一般
会比 Minor GC 慢 10 倍以上。

10.JDK 监控和故障处理工具：
  (1) jps (JVM Process Status Tool)：虚拟机进程状况工具，显示指定系统内所有的 HotSpot 虚拟机进程。
  命令格式：jps [options] [hostid]    // options：-q，-m，-l，-v; hostid：RMI 注册表中注册的主机名
  (2) jstat (JVM Statistics Monitoring Tool)：虚拟机统计信息监视工具，用于收集 HotSpot 虚拟机各方面的运行数据。
  命令格式：jstat [option vmid [interval[s|ms] [count] ] ]      // interval：查询间隔；count：查询次数
  (3) jinfo (Configuration Info for Java)：Java 配置信息工具，显示虚拟机配置信息。
  命令格式：jinfo [option] pid      
  (4) jmap (Memory Map for Java)：Java 内存映像工具，生成虚拟机的内存转储快照(heapdump文件)。
  命令格式：jmap [option] vmid    // options：-dump，-finalizerinfo，-heap，-histo，-permstat，-F
  (5) jhat (JVM Heap Analysis Tool)：虚拟机堆转储快照分析工具，用于分析 heapdump 文件，它会建立一个 HTTP/HTML 
服务器，让用户可以在浏览器上查看分析结果。
  (6) jstack (Stack Trace for Java)：Java 堆栈跟踪工具，显示虚拟机的线程快照(threaddump 或 javacore 文件)。
  命令格式：jstack [option] vmid
  (7) JConsole (Java Monitoring and Management Console)：Java 监视与管理控制台。
  (8) VisualVM (All-in-One Java Troubleshooting Tool)：多合一故障处理工具。

11.调优案例分析：
  (1) 高性能硬件上的程序部署策略：通过 64 位 JDK 来使用大内存；使用若干个 32 位虚拟机建立逻辑集群来利用硬件资源。
  (2) 集群间同步导致的内存溢出。
  (3) 堆外内存导致的溢出错误。
  (4) 外部命令导致系统缓慢。
  (5) 服务器 JVM 进程崩溃。
  (6) 不恰当的数据结构导致内存占用过大。
  (7) 由 Windows 虚拟内存导致的长时间停顿。
  
12.Class 文件格式：
  Class 文件格式采用一种类似于 C 语言结构体的伪结构来存储数据，该伪结构只有两种数据类型：无符号数和表。
  无符号数属于基本的数据类型，以 u1、u2、u4、u8 来分别代表 1 个字节、2 个字节、4 个字节和 8 个字节的无符号数，
无符号数可以用来描述数字、索引引用、数量值或者按照 UTF-8 编码构成字符串值。
  表是由多个无符号数或者其它表作为数据项构成的复合数据类型，所有表都习惯地以 “_info” 结尾。
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_Class.png" />

```java
13.Java 虚拟机解释器的基本执行模型：
   do {
      自动计算 PC 寄存器的值加 1;
      根据 PC 寄存器的指示位置，从字节码流中取出操作码;
      if (字节码存在操作数) 从字节码流中取出操作数;
      执行操作码所定义的操作;
   } while (字节码流长度 > 0);
   
14.类的生命周期包括：加载 (Loading)、验证 (Verification)、准备 (Preparation)、解析 (Resolution)、
初始化 (Initialization)、使用 (Using) 和卸载 (Unloading) 7 个阶段。其中验证、准备、解析 3 个部分
统称为连接 (Linking)。
  (1) 加载：
    ① 通过一个类的全限定名来获取定义此类的二进制字节流。
    ② 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。
    ③ 在内存中生成一个代表这个类的 java.lang.Class 对象，作为方法区这个类的各种数据的访问入口。
  (2) 验证：确保 Class 文件的字节流中包含的信息符合当前虚拟机的要求。
  验证阶段大致上会完成 4 个阶段的检验动作：文件格式验证、元数据验证、字节码验证、符号引用验证。
  (3) 准备：正式为类变量分配内存并设置类变量初始值的阶段，这些变量所使用的内存都将在方法区中进行分配。
  (4) 解析：将常量池内的符号引用替换为直接引用的过程。
  (5) 初始化：类初始化阶段是类加载过程的最后一步。初始化阶段是执行类构造器 <clinit>() 方法的过程。
双亲委派模型的实现：
  protected synchronized Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
     // 首先检查请求的类是否已经被加载过了
     Class c = findLoadedClass(name);
     if (c == null) {
        try {
           if (parent != null) {
              c = parent.loadClass(name, false);
           } else {
              c = findBootstrapClassOrNull(name);
           }
        } catch (ClassNotFoundException e) {
           // 如果父类加载器抛出 ClassNotFoundException，说明父类加载器无法完成加载请求
        }
        if (c == null) {
           // 在父类加载器无法加载的时候，再调用本身的 findClass 方法进行类加载
           c = findClass(name);
        }
     }
     if (resolve) {
        resolveClass(c);
     }
     return c;
  }
```
