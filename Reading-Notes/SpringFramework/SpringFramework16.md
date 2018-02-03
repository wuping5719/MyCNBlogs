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

78.使用 RMI 暴露服务。
   (1) 使用 RmiServiceExporter 暴露服务。
   RmiServiceExporter 显式地支持使用 RMI 调用器暴露任何非 RMI 的服务。
   <bean class="org.springframework.remoting.rmi.RmiServiceExporter">
      <!-- 不一定要与要输出的 bean 同名 -->
      <property name="serviceName" value="AccountService"/>
      <property name="service" ref="accountService"/>
      <property name="serviceInterface" value="example.AccountService"/>
      <!--  默认为 1199 -->
      <property name="registryPort" value="1199" />
   </bean>
   (2) 在客户端链接服务。
   <bean id="accountService" class="org.springframework.remoting.rmi.RmiProxyFactoryBean">
      <property name="serviceUrl" value="rmi://HOST:1199/AccountService"/>
      <property name="serviceInterface" value="example.AccountService"/>
   </bean>

79.使用 Hessian 或者 Burlap 通过 HTTP 远程调用服务。
   (1) 为 Hessian 配置 DispatcherServlet。
   (2) 使用 HessianServiceExporter 暴露你的 Bean。
   <bean name="accountExporter" class="org.springframework.remoting.caucho.HessianServiceExporter">
      <property name="service" ref="accountService"/>
      <property name="serviceInterface" value="example.AccountService"/>
   </bean>
   (3) 在客户端连接服务。
   (4) 对通过 Hessian 或 Burlap 暴露的服务使用 HTTP Basic 认证。
   <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping">
      <property name="interceptors" ref="authorizationInterceptor"/>
   </bean>
   <bean id="authorizationInterceptor"
            class="org.springframework.web.servlet.handler.UserRoleAuthorizationInterceptor">
      <property name="authorizedRoles" value="administrator,operator"/>
   </bean>

80.使用 HTTP 调用器暴露服务。
   (1) Exposing the service object.
   <bean name="accountExporter" class="org.springframework.remoting.httpinvoker.HttpInvokerServiceExporter">
      <property name="service" ref="accountService"/>
      <property name="serviceInterface" value="example.AccountService"/>
   </bean>
   (2) 在客户端连接服务。
   <bean id="httpInvokerProxy" class="org.springframework.remoting.httpinvoker.HttpInvokerProxyFactoryBean">
      <property name="serviceUrl" value="http://remotehost:8080/remoting/AccountService"/>
      <property name="serviceInterface" value="example.AccountService"/>
   </bean>

81.Web Services。
   (1) 使用 JAX-RPC 暴露基于 Servlet 的 Web 服务。
   Spring 为 JAX-RPC Servlet 的端点实现提供了一个方便的基类(ServletEndpointSupport)。
   (2) 使用 JAX-RPC 访问 Web 服务。
   Spring 提供了两个工厂 Bean 用来创建 Web 服务代理，LocalJaxRpcServiceFactoryBean 和 JaxRpcPortProxyFactoryBean。
   <bean id="accountWebService" class="org.springframework.remoting.jaxrpc.JaxRpcPortProxyFactoryBean">
      <property name="serviceInterface" value="example.RemoteAccountService"/>
      <property name="wsdlDocumentUrl" value="http://localhost:8080/account/services/accountService?WSDL"/>
      <property name="namespaceUri" value="http://localhost:8080/account/services/accountService"/>
      <property name="serviceName" value="AccountService"/>
      <property name="portName" value="AccountPort"/>
   </bean>
   (3) 注册 JAX-RPC Bean 映射。
   public class AxisPortProxyFactoryBean extends JaxRpcPortProxyFactoryBean {
      protected void postProcessJaxRpcService(Service service) {
         TypeMappingRegistry registry = service.getTypeMappingRegistry();
         TypeMapping mapping = registry.createTypeMapping();
         registerBeanMapping(mapping, Account.class, "Account");
         registry.register("http://schemas.xmlsoap.org/soap/encoding/", mapping);
      }
      protected void registerBeanMapping(TypeMapping mapping, Class type, String name) {
         QName qName = new QName("http://localhost:8080/account/services/accountService", name);
         mapping.register(type, qName, new BeanSerializerFactory(type, qName),
                new BeanDeserializerFactory(type, qName));
      }
   }
   (4) 注册自己的 JAX-RPC 处理器。
   public class AccountHandler extends GenericHandler {
      public QName[] getHeaders() {
         return null;
      }
      public boolean handleRequest(MessageContext context) {
         SOAPMessageContext smc = (SOAPMessageContext) context;
         SOAPMessage msg = smc.getMessage();
         try {
            SOAPEnvelope envelope = msg.getSOAPPart().getEnvelope();
            SOAPHeader header = envelope.getHeader();
            ...
         } catch (SOAPException ex) {
            throw new JAXRPCException(ex);
         }
         return true;
      }
   }
   (5) 使用 JAX-WS 暴露基于 Servlet 的 Web 服务。
   Spring 为 JAX-WS Servlet 端点实现提供了一个方便的基类(SpringBeanAutowiringSupport)。
   import org.springframework.web.context.support.SpringBeanAutowiringSupport;
   @WebService(serviceName="AccountService")
   public class AccountServiceEndpoint extends SpringBeanAutowiringSupport {
      @Autowired
      private AccountService biz;
      @WebMethod
      public void insertAccount(Account acc) {
         biz.insertAccount(acc);
      }
      @WebMethod
      public Account[] getAccounts(String name) {
         return biz.getAccounts(name);
      }
   }
   (6) 使用 JAX-WS 暴露单独 Web 服务。
   Spring 的 SimpleJaxWsServiceExporter 类检测所有在 Spring 应用上下文中配置的 @WebService 注解 bean，
然后通过默认的 JAX-WS 服务器(JDK 1.6 HTTP服务器)来暴露它们。
   <bean class="org.springframework.remoting.jaxws.SimpleJaxWsServiceExporter">
      <property name="baseAddress" value="http://localhost:9999/"/>
   </bean>
   (7) 使用 Spring 支持的 JAX-WS RI 来暴露服务。
   (8) 使用 JAX-WS 访问 web 服务。
   类似 JAX-RPC 支持，Spring 提供了 2 个工厂 bean 来创建 JAX-WS web 服务代理，
它们是 LocalJaxWsServiceFactoryBean 和 JaxWsPortProxyFactoryBean。
   <bean id="accountWebService" class="org.springframework.remoting.jaxws.JaxWsPortProxyFactoryBean">
      <property name="serviceInterface" value="example.AccountService"/>
      <property name="wsdlDocumentUrl" value="http://localhost:8080/account/services/accountService?WSDL"/>
      <property name="namespaceUri" value="http://localhost:8080/account/services/accountService"/>
      <property name="serviceName" value="AccountService"/>
      <property name="portName" value="AccountPort"/>
   </bean>
   (9) 使用 XFire 来暴露 Web 服务。
   <beans>
      <bean name="/Echo" class="org.codehaus.xfire.spring.remoting.XFireExporter">
        <property name="serviceInterface" value="org.codehaus.xfire.spring.Echo"/>
        <property name="serviceBean">
    	       <bean class="org.codehaus.xfire.spring.EchoImpl"/>
        </property>
        <!-- the XFire bean is defined in the xfire.xml file -->
        <property name="xfire" ref="xfire"/>
      </bean>
   </beans>
```
