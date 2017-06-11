<h2>《HotSpot 实战》 :books: </h2> 

> 陈涛 著       人民邮电出版社

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
```

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_VM2.png"> VM 与外界的通信方式 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_VMLifeCycle.png"> 虚拟机生命周期 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_OOPKlass.png"> 基于 OOP-Klass 的对象访问定位 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_ClassLoaders.png"> 类加载流程 </a>
