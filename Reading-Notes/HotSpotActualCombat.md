<h2>《HotSpot 实战》 :books: </h2> 

> 陈涛 著       人民邮电出版社

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_VM2.png"> VM 与外界的通信方式 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_VMLifeCycle.png"> 虚拟机生命周期 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_OOPKlass.png"> 基于 OOP-Klass 的对象访问定位 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_ClassLoaders.png"> 类加载流程 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_Linked.png"> 链接流程 </a>

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_FastAndSlow.png"> 实例的创建流程 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_RuntimeData.png"> 运行时数据区的职能划分 </a>

```c++
1.HotSpot 内核的顶层模块：
  (1) Adlc：平台描述文件。
  (2) Libadt：抽象数据结构。
  (3) Asm：汇编器。
  (4) Code：机器码生成。
  (5) C1：Client 编译器，即 C1 编译器。
  (6) Ci：动态编译器。
  (7) Compiler：调用动态编译器的接口。
  (8) Opto：Server 编译器，即 C2 编译器。
  (9) Shark：基于 LLVM 实现的即时编译器。
  (10) Interpreter：解释器。
  (11) Classfile：Class 文件解析和类的链接等。
  (12) Gc_interface：GC 接口。
  (13) Gc_implementation：垃圾收集器的具体实现。
  (14) Memory：内存管理。
  (15) Oops：JVM 内部对象表示。
  (16) Prims：HotSpot 对外接口。
   Prims 主要包括 4 个模块：JNI (Java Native Interface) 模块，Java 本地接口；JVM 模块，标准 JNI 接口的补充；
JVMTI 模块，Java 虚拟机工具接口，一种编程接口；Perf 模块，JDK 中 sun.misc.Perf 类的底层实现，监控虚拟机内部的
Perf Data 计数器。
  (17) Runtime：运行时。
  (18) Services：JMX 接口。
  (19) Utilizes：内部工具类和公共函数。
  
2.对象表示机制：
  OOP-Klass 二分模型：
  (1) OOP：ordinary object pointer。即普通对象指针，用来描述对象实例信息。
  (2) Klass：Java 类的 C++ 对等体，用来描述 Java 类。
  HotSpot 对象访问机制的要点：在对象引用中存放的是指向对象 (instanceOop) 的指针，对象本身则持有类 (instanceKlass)
的指针。

3.链接：
  符号引用转换成直接引用的过程，称为解析，也叫常量池解析。
  在 instanceKlass 类中定义了链接过程 link_class_impl()，主要步骤如下：
  (1) 若该 class 处于出错状态，则抛出 NoClassDefFoundError 异常。
  (2) 若该 class 已链接，则退出此流程并返回链接成功。
  (3) 在开始对该 class 对象链接前，对超类递归地执行这一过程，进行链接。
  (4) 对该 class 对象实现的所有接口递归地执行这一过程，进行链接。
  (5) 如果在链接超类的过程中，该 class 对象已链接，则退出此流程并返回链接成功。
  (6) 验证 (verfication)，即字节码验证。
  (7) 重写、重定位和方法链接。重写是为支持更好的解释器运行性能，向常量池添加缓存，并调整相应字节码的常量池索引
重新指向常量池 Cache 索引；重定位是 relocate_and_link_methods()，其中包含方法链接，是为 Java 方法配置编译器
或解释器入口。
  (8) 由于方法被重写后会产生新的 methodOops，在这里需呀初始化虚函数表 vtable 和接口表 itable。
  (9) 设置该类状态为已链接并返回。
  
4.运行时数据区：
  虚拟机启动时，会在内存中开辟空间并按职能划分为不同的区域，这些内存区域主要包括：堆用来分配 Java 对象空间；
方法区用来存储类元数据；线程私有的栈和 PC 等。
  方法区利用常量池和常量池 Cache 实现字段和方法的快速访问。在虚拟机内部使用 methodOop 表示一个 Java 方法，
已解析类中使用方法表容纳类定义的方法，字节码使用 constMethodOop 进行存储。
  为支持虚拟机性能监控，在虚拟机中开辟了一块共享内存，专门存放 PerfData 计数器，虚拟机使用共享内存方式向外部
进程提供了一种通信的手段，允许外部监控进程 attach 至虚拟机进程，从共享内存中得到 PerfData。
  虚拟机运行时数据还可以以另外一种方式完整地呈现给用户：转储。转储包括对整个应用程序转储、堆转储和线程转储，
转储信息为定位程序性能瓶颈或故障提供了一种离线分析的手段。

5.垃圾收集算法：
  (1) 标记-清除 (Mark-Sweep)，该算法可分为两个阶段。
  标记阶段：标记出所有可以回收的对象。
  清除阶段：回收所有已标记的对象，释放这部分空间。
  (2) 复制算法 (Copying)。
  划分区域：将内存区域按比例划分为 1 个 Eden 区作为分配对象的 “主战场” 和 2 个幸存区 (即 Survivor 空间，
划分为 2 个等比例的 from 区和 to 区)。
  复制：收集时，打扫 “战场”，将 Eden 区中仍存活的对象复制到某一块幸存区中。
  清除：由于上一阶段已确保仍存活的对象已被妥善安置，现在可以 “清理战场” 了，释放 Eden 区和另一块幸存区。
  晋升：若在 “复制” 阶段，一块幸存区接纳不了所有的 “幸存” 对象，则直接晋升到老年代。
  (3) 标记-压缩 (Mark-Compact)，该算法分为两个阶段。
  标记阶段：标记出所有可以回收的对象。
  压缩阶段：将标记阶段的对象移动到空间的另一端，释放剩余的空间。
  
6.Java 栈：
  JVM 根据跨平台的需求设计了一套围绕栈操作的指令集，栈是 JVM 操作的核心资源。JVM 栈由局部变量和操作数栈
组成，Java 程序在运行时，根据方法的调用会产生新的栈帧，并在程序运行期间不断的执行入栈和出栈操作，以实现
演算过程。在 JVM 内部调用 Java 方法都是通过 CallStub 模块来完成的。栈顶缓存是通过将最频繁使用的栈顶元素
缓存到硬件寄存器中，以减少对内存的访问的技术。

7.CMS (并发标记-清除，Concurrent Mark-Sweep) 收集器的完整收集过程：
  (1) 初始标记 (initial-mark)：从根对象节点仅扫描与根节点直接关联的对象并标记，这个过程必须 STW
(Stop The World)，但由于根对象数量有限，所以这个过程很短暂。
  (2) 并发标记 (concurrent-marking)：与用户线程并发进行。这个阶段紧随初始标记阶段，在初始标记的基础上
继续向下追溯标记。并发标记阶段，应用程序的线程和并发标记的线程并发执行，所以用户不会感到停顿。
  (3) 并发预清理 (concurrent-precleaning)：与应用线程并发进行。由于上一阶段执行期间，会出现一些趁机 “晋升”
到老年代的对象。在该阶段通过重新扫描，减少下一阶段 “重新标记” 的工作，因为下一阶段会 STW。
  (4) 重新标记 (remark)：STW，但很短暂。暂停工作线程，由 GC 线程扫描在 CMS 堆中的对象。
  (5) 并发清理 (concurrent-sweeping)：清理垃圾对象，这个阶段 GC 线程和应用线程并发执行。
  (6) 并发重置 (concurrent-reset)：这个阶段，重置 CMS 收集器的数据结构，做好下一次执行 GC 任务的准备工作。

8.G1 (Gabage First) 收集器的工作过程：
  (1) 初始标记 (Initial Mark)：STW。G1 将这个过程伴随在一次普通的新生代 GC 中完成。该阶段标记的是幸存区 Regions
(Root Regions)。当然，该区域仍有可能引用老年代的对象。
  (2) 根区域扫描 (Root Region Scanning)：扫描幸存区中引用老年代的 Regions。该阶段与应用程序并发进行。这一过程
必须能够在新生代 GC 发生前完成。
  (3) 并发标记 (Concurrent Marking)：找出全堆中存活对象。该阶段与应用程序并发进行。这一过程允许被新生代 GC 打断。
  (4) 重新标记 (Remark)：STW，完成堆中存活对象的标记。重新标记基于 SATB (snapshot-at-the-beginning) 算法，比
CMS 收集器算法快很多。
  (5) 清理 (Cleanup)：包括 3 个阶段：首先，计算活跃对象并完全释放自由 Regions (STW)；
然后，处理 Remembered Sets (STW)；最后，重置空闲 Regions 并将它们放回空闲列表 (并发)。
  (6) 复制 (Copying)：STW。将存活对象疏散或复制至新的未使用区域内。

9.在 HotSpot 的实现中，解释器的主要组成部分：
  (1) 解释器 (Interpreter)：解释执行的功能组件。在 HotSpot 中，实现了两种解释器，一种是虚拟机默认使用的模版解释器
(TemplateInterpreter)；另一种是 C++ 解释器 (CppInterpreter)。
  (2) 代码生成器 (Code Generator)：利用解释器的宏汇编器 (MASM) 向代码缓存空间写入生成的代码。
  (3) InterpreterCodelet：由解释器运行的代码片段。在 HotSpot 中，所有由代码生成器生成的代码都由一个 Codelet 来
表示。面向解释器的 Codelet 称为 InterpreterCodelet，由解释器进行维护。利用这些 Codelet，JVM 可完成在内部空间
中存储、定位和执行代码的任务。
  (4) 转发表 (Dispatch table)：为方便快速找到与字节码对应的机器码，模版解释器使用了转发表。它按照字节码顺序，包含
了所有字节码到机器码的关联信息。模版解释器拥有两张转发表，一张是正常模式表，另一张表用来使解释器进入 SafePoint。
转发表最大 256 个条目，这也是由单字节表示的字节码最大数量。
```
