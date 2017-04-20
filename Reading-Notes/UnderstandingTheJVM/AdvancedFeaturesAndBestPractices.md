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
<img src="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_JavaVM.png" />

```java
```
