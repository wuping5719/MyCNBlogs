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
```
