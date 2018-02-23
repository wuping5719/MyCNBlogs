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

86.接收消息。
   (1) 同步接收: 可重载的 receive(..) 方法提供同步接收消息功能。
   (2) 异步接收-消息驱动的 POJO: 类似于 EJB 世界里流行的消息驱动 Bean(MDB)，消息驱动 POJO(MDP) 作为 JMS 消息的接收器。
   import javax.jms.JMSException;
   import javax.jms.Message;
   import javax.jms.MessageListener;
   import javax.jms.TextMessage;
   public class ExampleListener implements MessageListener {
      public void onMessage(Message message) {
          if (message instanceof TextMessage) {
             try {
                System.out.println(((TextMessage) message).getText());
             } catch (JMSException ex) {
                throw new RuntimeException(ex);
             }
          } else {
             throw new IllegalArgumentException("Message must be of type TextMessage");
          }
      }
   }
   (3) SessionAwareMessageListener 接口: 是一个 Spring 专门用来提供类似于 JMS MessageListener 的接口，
也提供了从接收 Message 来访问 JMS Session 的消息处理方法。
   (4) MessageListenerAdapter: 是 Spring 的异步支持消息类中的不变类(final class)：简而言之，
它允许你几乎将任意 一个类做为 MDP 显露出来(当然有某些限制)。
   (5) 事务中的消息处理: 通过监听器容器定义中的 sessionTransacted 标记可以轻松的激活本地资源事务。
   <bean id="jmsContainer" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
      <property name="connectionFactory" ref="connectionFactory"/>
      <property name="destination" ref="destination"/>
      <property name="messageListener" ref="messageListener"/>
      <property name="sessionTransacted" value="true"/>
   </bean>

87.JCA 消息端点的支持。
   Spring 提供了基于 JCA MessageListener 容器的支持。JmsMessageEndpointManager 将根据供应者 ResourceAdapter 
的类名自动地决定 ActivationSpec 类名。
   <bean class="org.springframework.jms.listener.endpoint.JmsMessageEndpointManager">
      <property name="resourceAdapter" ref="resourceAdapter"/>
      <property name="jmsActivationSpecConfig">
         <bean class="org.springframework.jms.listener.endpoint.JmsActivationSpecConfig">
            <property name="destinationName" value="myQueue"/>
         </bean>
      </property>
      <property name="messageListener" ref="myMessageListener"/>
   </bean>
   
   <bean id="resourceAdapter" class="org.springframework.jca.support.ResourceAdapterFactoryBean">
       <property name="resourceAdapter">
           <bean class="org.apache.activemq.ra.ActiveMQResourceAdapter">
               <property name="serverUrl" value="tcp://localhost:61616"/>
           </bean>
       </property>
       <property name="workManager">
           <bean class="org.springframework.jca.work.SimpleTaskWorkManager"/>
       </property>
   </bean>

88.将 Bean 暴露为 JMX。
   Spring 的 JMX 框架的核心类是 MBeanExporter。这个类负责获取 Spring Bean，然后将其注册到一个 JMX MBeanServer 上。
   <bean id="exporter" class="org.springframework.jmx.export.MBeanExporter" lazy-init="false">
      <property name="beans">
         <map>
            <entry key="bean:name=testBean1" value-ref="testBean"/>
         </map>
      </property>
   </bean>
   (1) 创建 MBeanServer。
   声明式地将 org.springframework.jmx.support.MBeanServerFactoryBean 实例添加到你的配置里。
   (2) 重用原有的 MBeanServer。
   (3) 延迟初始化的 MBean。
   (4) MBean 的自动注册。
   (5) 控制注册行为：REGISTRATION_FAIL_ON_EXISTING，REGISTRATION_IGNORE_EXISTING，REGISTRATION_REPLACE_EXISTING。
   <bean id="exporter" class="org.springframework.jmx.export.MBeanExporter">
      <property name="beans">
          <map>
             <entry key="bean:name=testBean1" value-ref="testBean"/>
          </map>
      </property>
      <property name="registrationBehaviorName" value="REGISTRATION_REPLACE_EXISTING"/>
   </bean>

89.控制 Bean 的管理接口。
   (1) MBeanInfoAssembler 接口。
   MBeanExporter 在后台委托给了负责定义 Bean 管理接口的 org.springframework.jmx.export.assembler.MBeanInfoAssembler
的一个实现来管理暴露 Bean 的信息。
   缺省实现是 org.springframework.jmx.export.assembler.SimpleReflectiveMBeanInfoAssembler，
它仅定义了一个暴露所有 public 属性，public 方法的管理接口。
   (2) 使用源码级元数据。
   使用 MetadataMBeanInfoAssembler，你能够用源码级元数据给你的 Bean 定义管理接口。
org.springframework.jmx.export.metadata.JmxAttributeSource 封装了元数据的读取。
   (3) 使用 JDK 5.0 的注解。
   Spring 提供了一套相当于 Commons Attribute 注解和一个策略接口 JmxAttributeSource 的实现类
AnnotationsJmxAttributeSource，这个类可以让 MBeanInfoAssembler 读取这些注解。 
   (4) 源代码级的元数据类型: 将类的所有实例标识为 JMX 受控资源: @ManagedResource；
将方法标识为 JMX 操作：@ManagedOperation；将 getter 或者 setter 标识为部分 JMX 属性：@ManagedAttribute；
定义操作参数说明：@ManagedOperationParameter 和 @ManagedOperationParameters。
   (5) AutodetectCapableMBeanInfoAssembler 接口。
   AutodetectCapableMBeanInfo 现成的唯一实现是 MetadataMBeanInfoAssembler，
它“表决”将所有标识了 ManagedResource 属性的 Bean 包含在内。
   (6) 用 Java 接口定义管理接口。
   Spring 包含了 InterfaceBasedMBeanInfoAssembler，它可以根据一组接口定义的方法限定要暴露的方法和属性。 
   (7) 使用 MethodNameBasedMBeanInfoAssembler。
   MethodNameBasedMBeanInfoAssembler 允许你指定要暴露成 JMX 属性和操作的方法名称列表。

90.控制 Bean 的 ObjectName。
   对于每个 Bean 的注册，MBeanExporter 在后台委派给了 ObjectNamingStrategy 的一个实现来获取 ObjectName。 
缺省实现是 KeyNamingStrategy，它将默认使用 beans 上 Map 的键值作为 ObjectName。
KeyNamingStrategy 可以将该键值与 Properties 文件中的一个条目对应，以此解析 ObjectName。
除了 KeyNamingStrategy 之外，Spring 还提供了另外两个 ObjectNamingStrategy 的实现： 
基于 bean 的 JVM 标识构建 ObjectName 的 IdentityNamingStrategy；
利用源代码级元数据获取 ObjectName 的 MetadataNamingStrategy。 
   (1) 从 Properties 读取 Properties。
   (2) 使用 MetadataNamingStrategy。
   MetadataNamingStrategy 用每个 Bean 上 ManagedResource 属性的 objectName 属性来构建 objectName。
   (3) <context:mbean-export/> 元素。
   <context:mbean-export server="myMBeanServer" default-domain="myDomain"/>

91.JSR-160 连接器。
   Spring JMX 模块在 org.springframework.jmx.support 包内提供了两个 FactoryBean 实现，
用来构建服务器端和客户端的连接器。
   (1) 服务器端连接器。
   使 Spring JMX 构建，启动和暴露一个 JSR-160 JMXConnectorServer，要使用以下配置：
   <bean id="serverConnector" class="org.springframework.jmx.support.ConnectorServerFactoryBean"/>
   (2) 客户端连接器。
   要构建一个 MBeanServerConnection 到一个远程的 JSR-160 MBeanServer，
使用以下所示的 MBeanServerConnectionFactoryBean。 
   <bean id="clientConnector" class="org.springframework.jmx.support.MBeanServerConnectionFactoryBean">
      <property name="serviceUrl" value="service:jmx:rmi://localhost:9875"/>
   </bean>
   (3) 基于 Burlap/Hessian/SOAP 的 JMX。
   <bean id="serverConnector" class="org.springframework.jmx.support.ConnectorServerFactoryBean">
      <property name="objectName" value="connector:name=burlap"/>
      <property name="serviceUrl" value="service:jmx:burlap://localhost:9874"/>
   </bean>

92.通知。
   (1) 为通知注册监听器。
   Spring 的 JMX 支持使得用任意数量 MBean 注册任意数量的 NotificationListeners 监听器
(包括由 Spring 的 MBeanExporter 输出和其他机制注册的 MBean)都非常容易。
   (2) 发布通知。
   Spring 的 JMX 通知发布支持中的关键接口是 NotificationPublisher。任意要通过 MBeanExporter 实例输出为 MBean
的 Bean 都可以实现 NotificationPublisherAware 接口来获得对 NotificationPublisher 实例的访问。

93.配置 CCI。
   JCA(Java Connector Architecture)规范来标准化对 EIS(Enterprise Information System)的访问。
   SPI(Service Provider Interfaces)是连接器提供者(Connector Provider)必须实现的接口。
   CCI(Common Client Interface)是应用程序用来与连接器交互并与 EIS 通信的接口。
   (1) 在 Spring 中配置 ConnectionFactory。
   <bean id="eciConnectionFactory" class="org.springframework.jndi.JndiObjectFactoryBean">
      <property name="jndiName" value="eis/cicseci"/>
   </bean>
   (2) 配置 CCI 连接。
   JCA CCI 允许开发者使用自己的连接器的 ConnectionSpec 接口实现来配置到 EIS 的连接。
为了配置该连接的属性，需要用一个指定的 ConnectionSpecConnectionFactoryAdapter 适配器来封装目标连接工厂。
   (3) 使用一个 CCI 单连接。
   SingleConnectionFactory 适配器类将延迟打开一个单独的连接并在应用程序销毁这个 Bean 的时候关闭它。
这个类将暴露出特殊 Connection 的相应代理，并共享同一个底层的物理连接。 

94.使用 Spring 的 CCI 访问支持。
   (1) 记录转换。
   通过使用 Spring 的 CciTemplate 来指定创建记录并从记录中提取数据的策略。 
   (2) CciTemplate 类。
   CciTemplate 类是 CCI 核心支持包(org.springframework.jca.cci.core)中主要的类。 
   CciTemplate.execute(..) 可以以下列两种方式使用：
   ① 在提供直接的 Record 参数的情况下，只需要传递输入记录给 CCI，而返回的对象就是对应的 CCI 输出记录。
   ② 在提供使用记录映射的应用对象的情况下，需要提供相应的 RecordCreator 和 RecordExtractor 实例。
   (3) DAO 支持: Spring 的 CCI 支持为 DAO 提供了一个抽象类 CciDaoSupport，支持 ConnectionFactory
或 CciTemplate 实例的注入。
   (4) 自动输出记录生成。
   设定 CciTemplate 类的 outputRecordCreator 属性来自动生成一个输出记录，当接收到响应时 JCA 连接器
(JCA connector)将填充该记录并返回给模板的调用者。 
   (5) 直接使用一个 CCI Connection 接口和 Interaction 接口。
   ConnectionCallback 接口提供以 CCI Connection 作为参数，为了在它上面执行自定义动作，添加了创建 Connection 
的 CCI ConnectionFactory。后者在获取相关 RecordFactory 实例和创建 indexed/mapped records 时很有用。

95.建模 CCI 访问为操作对象。
   (1) MappingRecordOperation。
   提供了两个模板方法来指明如何将一个输入对象转换为输入记录，以及如何将一个输出记录转换为输出对象(记录映射)： 
   createInputRecord(..) 指定了如何将一个输入对象转换为输入 Record;
   extractOutputData(..) 指定了如何从输出 Record 中提取输出对象。
   (2) MappingCommAreaOperation。
   扩展了 MappingRecordOperation 类以提供这种专门的 CommArea 支持。 它隐含地使用了 CommAreaRecord 类
作为输入和输出 Record 类型， 并提供了两个新的方法来转换输入对象到输入CommArea，以及转换输出 CommArea 到输出对象。
```
