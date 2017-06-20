<h2>《分布式服务框架—原理与实践》 :books: </h2> 

> 李林锋 著       中国工信出版集团   电子工业出版社

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_RPCFramework.png"> RPC 框架原理 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_ServiceArchitecture.png"> 分布式服务框架的逻辑架构 </a>

```java
1.分布式服务框架的架构可以抽象为 3 层：
  (1) RPC 层：包括底层通信框架 (例如：NIO 框架的封装、公有协议的封装等)、序列化和反序列化框架、用于屏蔽底层通信协议细节
和序列化方式差异的 Remoting 框架。
  (2) Filter Chain 层：服务调用职责链，提供多种服务调用切面供框架自身和使用者扩展，例如：负载均衡、服务调用性能统计、
服务调用完成通知机制、失败重发等。
  (3) Service 层：主要包括 Java 动态代理，消费者使用，主要用于将服务提供者的接口封装成远程服务调用；Java 反射，服务
提供者使用，根据消费者请求消息中的接口名、方法名、参数列表反射调用服务提供者的接口本地实现类。再向上就是业务的服务接口
定义和实现类，对于使用 Spring 配置化开发的就是 Spring Bean，服务由业务来实现，平台负责将业务接口发布成远程服务。

2.开源 NIO 框架 Netty 的优势：
  (1) API 使用简单，开发门槛低。
  (2) 功能强大，预置了多种编解码功能，支持多种编解码功能，支持多种主流协议。
  (3) 定制能力强，可以通过 ChannelHandler 对通信框架进行灵活地扩展。
  (4) 性能好，通过与其它业界主流的 NIO 框架对比，Netty 的综合性能最优。
  (5) 成熟、稳定，Netty 修复了已经发现的所有 JDK NIO Bug，业务开发人员不需要再为 NIO 的 Bug 而烦恼。
  (6) 社区活跃，版本迭代周期短，发现的 Bug 可以被及时修复。
  (7) 经历了大规模的商业应用考验，质量得到验证。

3.如何解决 MessagePack 的读半包问题？
  public void initChannel (SocketChannel ch) throws Exception {
      ch.pipeline().addLast("frameDecoder", new LengthFieldBasedFrameDecoder(65535, 0, 2, 0, 2));
      ch.pipeline().addLast("msgpack decoder", new MsgpackDecoder());
      ch.pipeline().addLast("frameEncoder", new LengthFieldPrepender(2));
      ch.pipeline().addLast("msgpack encoder", new MsgpackEncoder());
      ch.pipeline().addLast(new EchoClientHandler(sendNumber));
  }
```
