<h2>《HotSpot 实战》 :books: </h2> 

> 陈涛 著       人民邮电出版社

```java
1.HotSpot 内核﻿的顶层模块：
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
```

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_VM2.png"> VM 与外界的通信方式 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
