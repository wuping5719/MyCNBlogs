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
```
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_GC.png" />

```java
```
