<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
83.JMS (Java Message Service)。
   (1) org.springframework.jms.core 包提供使用 JMS 的核心功能。
   (2) org.springframework.jms.support 包提供 JMSException 的转换功能。
   (3) org.springframework.jms.support.converter 包提供一个 MessageConverter 用来抽象 Java 对象
和 JMS 消息之间的转换操作。
   (4) org.springframework.jms.support.destination 包为管理 JMS 目的地提供多种策略，
例如为存储在 JNDI 中的目的地提供一个服务定位器。 
   (5) org.springframework.jms.connection 包提供一个适合在独立应用中使用的 ConnectionFactory 的实现。

84.使用 Spring JMS。
   (1) JmsTemplate: JMS API 有两种发送方法，一种采用发送模式、优先级和存活时间作为服务质量 (QOS) 参数，
另一种使用无需 QOS 参数的缺省值方法。
   (2) 连接工厂: JmsTemplate 需要一个对 ConnectionFactory 的引用。ConnectionFactory 是 JMS 规范的一部分，
并且是使用 JMS 的入口。SingleConnectionFactory 通常引用一个来自 JNDI 的标准 ConnectionFactory。
   (3) 目的地管理: 目的地是可以在 JNDI 中存储和获取的 JMS 管理的对象。配置一个 Spring 应用上下文时，
可以使用 JNDI 工厂类 JndiObjectFactoryBean 把对你对象的引用依赖注入到 JMS 目的地中。
   (4) 消息侦听容器: Spring 提供了三种 AbstractMessageListenerContainer 的子类: SimpleMessageListenerContainer、
DefaultMessageListenerContainer、ServerSessionMessageListenerContainer。
   (5) 事务管理: Spring 提供了 JmsTransactionManager 为单个 JMS ConnectionFactory 管理事务。

85.发送消息。
```
