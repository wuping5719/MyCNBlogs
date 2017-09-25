<h2>《Spring In Action》 :books: </h2> 

> [美] 沃尔斯 ● 布雷登巴赫 著    人民邮电出版社

```java
1.Spring 远程调用支持 6 种不同的 RPC 模式：远程方法调用（RMI）、Caucho 的 Hessian 和 Burlap、
Spring 自己的 HTTP invoker、EJB 和使用 JAX-RPC 的 Web Services。

2.连接 RMI 服务。
  访问支付服务的一种方法是写一个工厂方法，用传统的 RMI 方法取得对支付服务的引用。
  private String payServiceUrl = "rmi:/creditswitch/PaymentService";
  public PaymentService lookupPaymentService() throws RemoteException, NotBoundException, 
                MalformedURLException {
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
  
3.输出 RMI 服务。
  用传统方式 (非 Spring) 实现作为 RMI 服务的支付服务。
  public class PaymentServiceImpl extends UnicastRemoteObject implements PaymentService {
     public PaymentServiceImpl() throws RemoteException {}
     public String authorizeCreditCard(String creditCardNumber, String cardHolderName, int expirationMonth,
                       int expirationYear, float amount) throws AuthorizationException, RemoteException {
        String authCode = ...;
        // implement authorization
        return authCode;
     }
     public void settlePayment(String authCode, int accountNumber,
                        float amount) throws SettlementException, RemoteException {
        // implement settlement
     }
  }

  public interface PaymentService extends Remote {
     public String authorizeCreditCard(String cardNumber, String cardHolderName, int expireMonth,
             int expireYear, float amount) throws AuthorizationException, RemoteException;
     public void settlePayment(String authCode, int merchantNumber,
             float amount) throws SettlementException, RemoteException;
  }

  try {
     PaymentService paymentService = new PaymentServiceImpl();
     Registry registry = LocateRegistry.createRegistry(1099);
     Naming.bind("PayService", paymentService);
  } catch (RemoteException e) {
     …
  } catch (MalformedURLException e) {
     …
  }

4.访问 Hessian / Burlap 服务。
  <bean id="paymentService" class="org.springframework.remoting.caucho.HessianProxyFactoryBean">
      <property name="serviceUrl">
          <value>http://${serverName}/${contextPath}/pay.service</value>
      </property>
      <property name="serviceInterface">
          <value>com.springinaction.payment.PaymentService</value>
      </property>
  </bean>

  <bean id="paymentService" class="org.springframework.remoting.caucho.BurlapProxyFactoryBean">
      <property name="serviceUrl">
          <value>http://${serverName}/${contextPath}/pay.service</value>
      </property>
      <property name="serviceInterface">
          <value>com.springinaction.payment.PaymentService</value>
      </property>
  </bean>

5.用 Hessian 或 Burlap 公开 Bean 的功能。
 (1) 输出一个 Hessian 服务。
 <bean name="hessianPaymentService" class="org.springframework.remoting.caucho.HessianServiceExporter">
    <property name="service">
        <ref bean="paymentService"/>
    </property>
    <property name="serviceInterface">
        <value>com.springinaction.payment.PaymentService</value>
    </property>
 </bean>

 (2) 配置 Hessian 控制器。
 <bean id="urlMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="mappings">
       <props>
          <prop key="/pay.service">hessianPaymentService</prop>
       </props>
    </property>
 </bean>

 web.xml
 <servlet>
    <servlet-name>credit</servlet-name>
    <servlet-class>
        org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
    <load-on-startup>1</load-on-startup>
 </servlet>

 <servlet-mapping>
    <servlet-name>credit</servlet-name>
    <url-pattern>*.service</url-pattern>
 </servlet-mapping>

 (3) 输出一个 Burlap 服务。
 <bean name="burlapPaymentService" class="org.springframework.remoting.caucho.BurlapServiceExporter">
    <property name="service">
       <ref bean="paymentService"/>
    </property>
    <property name="serviceInterface">
       <value>com.springinaction.payment.PaymentService</value>
    </property>
 </bean>

6.使用 HTTP invoker。
 (1) 通过 HTTP 访问服务。
 <bean id="paymentService" class="org.springframework.remoting.httpinvoker.HttpInvokerProxyFactoryBean">
    <property name="serviceUrl">
       <value>http://${serverName}/${contextPath}/pay.service</value>
    </property>
    <property name="serviceInterface">
       <value>com.springinaction.payment.PaymentService</value>
    </property>
 </bean>

 (2) 把 Bean 作为 HTTP 服务公开。
 <bean id="httpPaymentService" class="org.springframework.remoting.httpinvoker.HttpInvokerServiceExporter">
    <property name="service">
        <ref bean="paymentService"/>
    </property>
    <property name="serviceInterface">
        <value>com.springinaction.payment.PaymentService</value>
    </property>
 </bean>

 <bean id="urlMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="mappings">
       <props>
          <prop key="/pay.service">httpPaymentService</prop>
       </props>
    </property>
 </bean>

 web.xml
 <servlet>
    <servlet-name>credit</servlet-name>
    <servlet-class>
        org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
    <load-on-startup>1</load-on-startup>
 </servlet>

 <servlet-mapping>
    <servlet-name>credit</servlet-name>
    <url-pattern>*.service</url-pattern>
 </servlet-mapping>

7.代理 EJB。
  Spring 提供了两个代理工厂 Bean，来代理 EJB 的访问：
   ① LocalStatelessSessionProxyFactoryBean —— 用来访问本地 EJB（EJB 和它的客户端在同一个容器中）。
   ② SimpleRemoteStatelessSessionProxyFactoryBean —— 用来访问远程 EJB（EJB 和它的客户端在独立的容器中）。

  <bean id="paymentService" class="org.springframework.ejb.access.LocalStatelessSessionProxyFactoryBean" 
                         lazy-init="true">
      <property name="jndiName">
         <value>payService</value>
      </property>
      <property name="businessInterface">
         <value>com.springinaction.payment.PaymentService</value>
      </property>
  </bean>

  <bean id="studentService" class="com.springinaction.training.service.StudentServiceImpl">
     …
     <property name="paymentService">
        <ref bean="paymentService"/>
     </property>
     …
  </bean>
```
