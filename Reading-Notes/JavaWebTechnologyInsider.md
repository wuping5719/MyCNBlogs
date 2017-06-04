<h2>《深入分析 Java Web 技术内幕》 :books: </h2> 

> 许令波 著       电子工业出版社

```java
1.当一个用户在浏览器里输入 www.taobao.com 这个 URL，将会会发生什么？
  首先请求 DNS 把这个域名解析成对应的 IP 地址，然后根据这个 IP 地址在互联网上找到对应的服务器，向这个服务器发起
一个 get 请求，由这个服务器决定返回默认的数据资源给访问的用户。在服务器端实际上还有很多复杂的业务逻辑：服务器
可能有很多台，到底指定哪台服务器来处理请求，这需要一个负载均衡设备来平均分配所有用户请求；还有请求的数据是存储
在分布式缓存里还是一个静态文件中，或是在数据库里；当数据返回浏览器时，浏览器解析数据发现还有一些静态资源 
(如 CSS、JS 或者图片) 时又会发起另外的 HTTP 请求，而这些请求很可能会在 CDN 上，那么 CDN 服务器又会处理这个
用户的请求，大体上一个用户请求会涉及这么多的操作。
```

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_CDN.png"> B/S 网络架构 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_DNS.png"> DNS 域名解析 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_CDNs.png"> CDN 架构 </a>

```java
2.Javac 编译器主要有四大模块：词法分析器、语法分析器、语义分析器和代码生成器。
```

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_Javac.png"> Javac 组件 </a>

```java
3.GC 日志格式：
  [GC [<collector>: <starting occupancy1> -> <ending occupancy1> (total size1), <pause time1> secs]
                   <starting occupancy2> -> <ending occupancy2> (total size2), <pause time2> secs]
格式说明：
  (1) <collector> 表示收集器的名称。
  (2) <starting occupancy1> 表示 Young 区在 GC 前占用的内存。
  (3) <ending occupancy1> 表示 Young 区在 GC 后占用的内存。
  (4) <pause time1> 表示 Young 区局部收集时 JVM 暂停处理的时间。
  (5) <starting occupancy2> 表示 JVM Heap 在 GC 前占用的内存。
  (6) <ending occupancy2> 表示 JVM Heap 在 GC 后占用的内存。
  (7) <pause time2> 表示 GC 过程中 JVM 暂停处理的总时间。
可以根据日志判断是否有内存泄漏，如果 <ending occupancy1> - <starting occupancy1> = 
<ending occupancy2> - <starting occupancy2>，则表明这次 GC 对象 100% 被回收，没有对象进入 Old 区或者 Perm 区。
如果等号左边的值大于等号右边的值，那么差值就是这次回收对象进入 Old 区或者 Perm 区的大小。如果随着时间的延长
<ending occupancy2> 的值一直在增长，而且 Full GC 很频繁，那么很可能就是内存泄露了。

4.分布式 Session 框架。
 (1) Session 的配置：
    <session>
       <key>sessinonID</key>
       <cookiekey>sessinonID</cookiekey>
       <lifeCycle>9000</lifeCycle>
       <base64>true</base64>
    </session>
  (2) Cookie 的配置：
    <cookie>
       <key>cookie</key>
       <lifeCycle></lifeCycle>
       <type>1</type>
       <path>/wp<path>
       <domain>xulingbo.net</domain>
       <decrypt>false</decrypt>
       <httpOnly>false</httpOnly>
    </cookie>
  (3) 跨域名共享 Cookie (跨域名同步 Session):
  需要一个跳转应用，这个应用可以被一个或多个域名访问，它的主要功能是从一个域名下取得 sessionID，然后将这个
sessionID 同步到另一个域名下。这个 sessionID 其实就是一个 Cookie，相当于我们经常遇到的 JSESSIONID，
所以要实现两个域名下的 Session 同步，必须要将同一个 sessionID 作为 Cookie 写到两个域名下。
```

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_SessionFrame.png"> 分布式 Session 框架的架构 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_JVM.png"> JVM 体系结构 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_Request.png"> Request 在容器中的路由 </a>

```java
5.JVM 的体系结构：
  JVM 的结构基本上由 4 部分组成：
  (1) 类加载器，在 JVM 启动时或者在类运行时将需要的 class 加载到 JVM 中。
  (2) 执行引擎，任务是负责执行 class 文件中包含的字节码指令，相当于实际机器上的 CPU。
  (3) 内存区，将内存划分成若干个区模拟实际机器上的存储、记录和调度功能模块，如实际机器上的各种功能的寄存器
或者　PC 指针的记录器等。
  (4) 本地方法调用，调用 C 或 C++ 实现的本地方法代码返回结果。
```
