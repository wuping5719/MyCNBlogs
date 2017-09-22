<h2>《Spring In Action》 :books: </h2> 

> [美] 沃尔斯 ● 布雷登巴赫 著    人民邮电出版社

```Java
1.Spring 远程调用支持 6 种不同的 RPC 模式：远程方法调用（RMI）、Caucho 的 Hessian 和 Burlap、
Spring 自己的 HTTP invoker、EJB 和使用 JAX-RPC 的 Web Services。

2.连接 RMI 服务。
  访问支付服务的一种方法是写一个工厂方法，用传统的 RMI 方法取得对支付服务的引用。
  private String payServiceUrl = "rmi:/creditswitch/PaymentService";
  public PaymentService lookupPaymentService() throws RemoteException, NotBoundException, MalformedURLException {
     PaymentService payService = (PaymentService) Naming.lookup(payServiceUrl);
     return payService;
  }
  引用一个 RMI PaymentService 的配置：
  <bean id="paymentService" class="org.springframework.remoting.rmi.RmiProxyFactoryBean">
     <property name="serviceUrl">
         <value>rmi://${paymenthost}/PayService</value>
     </property>
     <property name="serviceInterface">
         <value>com.springinaction.payment.PaymentService</value>
     </property>
  </bean>
```
