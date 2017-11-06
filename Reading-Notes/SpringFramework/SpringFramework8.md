<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
38.单元测试。
  (1) Mock 对象。
  org.springframework.mock.jndi 包里有一个 JNDI SPI 的实现，
它可以用来搭建一个为测试套件或单机应用所使用的简单 JNDI 环境。
  org.springframework.mock.web 包有一组 Servlet API mock 对象，
主要面向 Spring Web MVC 框架，能方便的测试 web 上下文和控制器。
  org.springframework.mock.web.portlet 包有一组 Portlet API mock 对象，面向 Spring Portlet MVC 框架。
  (2) 单元测试支持类。
  org.springframework.test.util 包内有 ReflectionTestUtils。
它是基于反射的工具方法集，用在单元测试和集成测试场景中。

39.集成测试。
```
