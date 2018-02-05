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
   (1) 使用 1.0.2 版的 JMS 实现发送消息到一个队列。
   import javax.jms.ConnectionFactory;
   import javax.jms.JMSException;
   import javax.jms.Message;
   import javax.jms.Queue;
   import javax.jms.Session;
   import org.springframework.jms.core.MessageCreator;
   import org.springframework.jms.core.JmsTemplate;
   import org.springframework.jms.core.JmsTemplate102;
   public class JmsQueueSender {
      private JmsTemplate jmsTemplate;
      private Queue queue;
      public void setConnectionFactory(ConnectionFactory cf) {
         this.jmsTemplate = new JmsTemplate102(cf, false);
      }
      public void setQueue(Queue queue) {
         this.queue = queue;
      }
      public void simpleSend() {
         this.jmsTemplate.send(this.queue, new MessageCreator() {
             public Message createMessage(Session session) throws JMSException {
               return session.createTextMessage("hello queue world");
             }
         });
      }
   }

   (2) 使用消息转换器：缺省的实现 SimpleMessageConverter 支持 String 和 TextMessage，byte[] 和 BytesMesssage，
以及 java.util.Map 和 MapMessage 之间的转换。
    public void sendWithConversion() {
       Map map = new HashMap();
       map.put("Name", "Nick");
       map.put("Age", new Integer(47));
       jmsTemplate.convertAndSend("testQueue", map, new MessagePostProcessor() {
          public Message postProcessMessage(Message message) throws JMSException {
             message.setIntProperty("AccountID", 1234);
             message.setJMSCorrelationID("123-00001");
             return message;
          }
       });
    }

   (3) SessionCallback 和 ProducerCallback: 接口 SessionCallback 和 ProducerCallback 分别提供了 JMS Session
和 Session / MessageProducer 对。JmsTemplate 上的 execute() 方法执行这些回调方法。 
```
