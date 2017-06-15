<h2>《Spring 源码深度解析》 :books: </h2> 

> 郝佳 著       人民邮电出版社
 
```java
1.Spring 的整体架构：
  (1) Core Container：核心容器包含有 Core、Beans、Context 和 Expression Language 模块。
  Core 和 Beans 模块是框架的基础部分，提供 IoC (控制反转) 和依赖注入特性。BeanFactory 提供对 Factory 模式的经典
实现来消除对程序性单例模式的需要，并真正地允许你从程序逻辑中分离出依赖关系和配置。
   ① Core 模块主要包含 Spring 框架基本的核心工具类，Spring 的其他组件都要使用到这个包里的类，Core 模块是其它组件
的基本核心。
   ② Beans 模块是所有应用都要用到的，它包含访问配置文件、创建和管理 bean 以及进行 Inversion of Control / 
Dependency Injection (IoC / DI) 操作相关的所有类。
   ③ Context 模块构建于 Core 和 Beans 模块基础之上，提供了一种类似于 JNDI 注册器的框架式的对象访问方法。Context 
模块继承了 Beans 的特性，为 Spring 核心提供了大量扩展，添加了对国际化 (例：资源绑定)、事件传播、资源加载和对 
Context 的透明创建的支持。Context 模块同时也支持 J2EE 的一些特性，例如 EJB、JMX 和基础的远程处理。
ApplicationContext 接口是 Context 模块的关键。
   ④ Expression Language 模块提供了一个强大的表达式语言用于在运行时查询和操纵对象。它是 JSP 2.1 规范中定义的
unifed expression language 的一个扩展。该语言支持设置/获取属性的值、属性的分配、方法的调用、访问数组上下文、
容器和索引器、逻辑和算术运算符、命名变量以及从 Spring 的 IoC 容器中根据名称检索对象。它也支持 list 投影、选择
和一般的 list 聚合。
 (2) Data Access / Integration：该层包含 JDBC、ORM、OXM、JMS 和 Transaction 模块。
   ① JDBC 模块提供一个 JDBC 抽象层，它可以消除冗长的 JDBC 编码和解析数据库厂商特有的错误代码。这个模块包含了 
Spring 对 JDBC 数据访问进行封装的所有类。
   ② ORM 模块为流行的对象-关系映射 API，如 JPA、JDO、Hibernate、iBatis 等，提供了一个交互层。利用 ORM 封装包，
可以混合使用所有 Spring 提供的特性进行 O/R 映射。如：简单声明性事务管理。
   ③ OXM 模块提供了一个对 Object/XML 映射实现的抽象层，Object/XML 映射实现包括 JAXB、Castor、XMLBeans、JiBX
和 XStream。
   ④ JMS (Java Messaging Service) 模块主要包含了一些制造和消费消息的特性。
   ⑤ Transaction 模块支持编程和声明性的事务管理，这些事务类必须实现特定的接口，并且对所有的 POJO 都适用。
  (3) Web：Web 上下文模块
```
