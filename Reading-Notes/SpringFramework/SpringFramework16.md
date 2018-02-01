<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
77.Spring 支持四种远程技术。
   1) 远程方法调用(RMI)。通过使用 RmiProxyFactoryBean 和 RmiServiceExporter，Spring 同时支持传统的 RMI
(使用 java.rmi.Remote 接口和 java.rmi.RemoteException)和通过 RMI 调用器实现的透明远程调用(支持任何Java接口)。
   2) 使用 HTTP 调用器暴露服务。
    (1) Spring 的 HTTP 调用器。Spring 提供了一种允许通过 HTTP 进行 Java 串行化的特殊远程调用策略，
它支持任意 Java 接口(就像 RMI 调用器)。相对应的支持类是 HttpInvokerProxyFactoryBean 和 HttpInvokerServiceExporter。
    (2) Hessian。通过 HessianProxyFactoryBean 和 HessianServiceExporter，可以使用 Caucho 提供的
基于 HTTP 的轻量级二进制协议来透明地暴露服务。
    (3) Burlap。Burlap 是 Caucho 基于 XML 用来替代 Hessian 的项目。Spring 提供了诸如 BurlapProxyFactoryBean
和 BurlapServiceExporter 的支持类。
   3) Web Services。
    (1) JAX RPC。Spring 通过 JAX-RPC(J2EE 1.4's wweb service API)为 Web services 提供远程服务支持。
    (2) JAX-WS。 Spring 通过(在Java EE 5 和 Java 6 中引入的 JAX-RPC 继承)为远程 Web Services 提供支持。
   4) JMS。通过 JmsInvokerServiceExporter 和 JmsInvokerProxyFactoryBean 使用 JMS 做为底层协议提供远程服务。
```
