<h2>《Spring 源码深度解析》 :books: </h2> 

> 郝佳 著       人民邮电出版社

<a href="http://images.cnblogs.com/cnblogs_com/wp5719/936332/o_RelatedBeanFactory.png"> 容器加载相关类 </a>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;

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
  (3) Web：Web 上下文模块建立在应用程序上下文模块之上，为基于 Web 的应用程序提供了上下文。Spring 框架支持与
Jakarta Struts 的集成。Web 层包含了 Web、Web-Servlet、Web-Struts 和 Web-Porlet 模块。
   ① Web 模块：提供了基础的面向 Web 的集成特性。例如：多文件上传、使用 servlet listeners 初始化 IoC 容器以及
一个面向 Web 的应用上下文。它还包含 Spring 远程支持中 Web 的相关部分。
   ② Web-Servlet 模块 web.servlet.jar：该模块包含 Spring 的 model-view-controller (MVC) 实现。Spring 的
MVC 框架使得模型范围内的代码和 web forms 之间能够清楚地分离开来，并与 Spring 框架的其它特性集成在一起。
   ③ Web-Struts 模块：该模块提供了对 Struts 的支持，使得类在 Spring 应用中能够与一个典型的 Struts Web 层集成
在一起。注意：该支持在 Spring 3.0 是 deprecated 的。
   ④ Web-Porlet 模块：提供了用于 Porlet 环境和 Web-Servlet 模块的 MVC 的实现。
  (4) AOP：AOP 模块提供了一个符合 AOP 联盟标准的面向切面编程的实现，它让你可以定义方法拦截点和切点，
从而将逻辑代码分开，降低它们之间的耦合性。利用 source-level 的元数据功能，还可以将各种行为信息合并到你的代码中，
这有点像 .Net 技术中的 attribute 概念。
   ① Aspects 模块提供了对 AspectJ 的集成支持。
   ② Instrumentation 模块提供了 class instrumentation 支持和 classloader 实现，使得可以在特定的
应用服务器上使用。
  (5) Test：Test 模块支持使用 JUnit 和 TestNG 对 Spring 组件进行测试。
```
