<h2>《Spring In Action》 :books: </h2> 

> [美] Craig Walls 著    人民邮电出版社

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
  
  <bean id="paymentService" class="org.springframework.ejb.access.SimpleRemoteStatelessSessionProxyFactoryBean"
                  lazy-init="true">
     <property name="jndiName">
         <value>payService</value>
     </property>
     <property name="businessInterface">
        <value>com.springinaction.payment.PaymentService</value>
     </property>
  </bean>

8.用 Spring 开发 EJB。
  Spring 提供了 4 种抽象支持类:
   (1) AbstractMessageDrivenBean —— 对开发从 JMS 之外的其他消息源接收消息的 message-driven Bean 有帮助。
   (2) AbstractJmsMessageDrivenBean —— 对开发从 JMS 源接收消息的 message-driven Beans 有帮助。
   (3) AbstractStatefulSessionBean —— 对开发有状态 EJB 有用。
   (4) AbstractStatelessSessionBean —— 对开发无状态 EJB 有用。
  这些抽象类通过两种方式简化 EJB 开发：
   a.它们提供了 EJB 生命周期方法的默认的空的实现（例如：ejbActivate()，ejbPassivate()，ejbRemove()）。
这些方法在每个 EJB 规范中都需要，但是通常都是作为空方法实现的。
   b.它们提供了对 Spring Bean 工厂的访问。这让你有可能把 EJB 实现成一个代理，
由 EJB 将业务逻辑的责任委托给由 Spring 配置的 POJO 来做。

public class CourseServiceEjb extends AbstractStatelessSessionBean implements CourseService {
   private CourseService courseService;
   
   protected void onEjbCreate() {
      courseService = (CourseService) getBeanFactory().getBean("courseService");
   }
   
   public Course getCourse(Integer id) {
      return courseService.getCourse(id);
   }
   
   public void createCourse(Course course) {
      courseService.createCourse(course);
   }

   public Set getAllCourses() {
      return courseService.getAllCourses();
   }

   public void enrollStudentInCourse(Course course, Student student) throws CourseException {
      courseService.enrollStudentInCourse(course, student);
   }
}

public void setSessionContext(SessionContext sessionContext) {
    super.setSessionContext(sessionContext);
    setBeanFactoryLocatorKey("java:comp/env/ejb/MyBeanFactory");
}

9.用 JAX-RPC 应用一个 Web Service。
  Bable Fish web service 的远程服务接口:
  import java.rmi.Remote;
  import java.rmi.RemoteException;
  public interface BabelFishRemote extends Remote {
    public String BabelFish(String translationMode, String sourceData) throws RemoteException;
  }

  String wsdlDocumentUrl = "http://www.xmethods.com/sd/2001/BabelFishService.wsdl";
  String namespaceUri = "http://www.xmethods.net/sd/BabelFishService.wsdl";
  String serviceName = "BabelFishService";
  String portName = "BabelFishPort";
  QName serviceQN = new QName(namespaceUri, serviceName);
  QName portQN = new QName(namespaceUri, portName);
  ServiceFactory sf = ServiceFactory.newInstance();
  Service service = sf.createService(new URL(wsdlDocumentUrl), serviceQN);
  BabelFishRemote babelFish = (BabelFishRemote) service.getPort(BabelFishRemote.class, portQN);

  String translated1 = babelFish.BabelFish("en_es", "Hello World");
  String translated2 = babelFish.BabelFish("es_fr", "Hola Mundo");
  String translated3 = babelFish.BabelFish("fr_de", "Bonjour Monde");

  Context ic = new InitialContext();
  BabelFishService babelFishService = (BabelFishService) ic.lookup("java:comp/env/service/BabelFish");
  BabelFishRemote babelFish = (BabelFishRemote) babelFishService.getBabelFishPort();

10.在 Spring 里置入一个 Web Service。
  <bean id="babelFish" class="org.springframework.remoting.jaxrpc.JaxRpcPortProxyFactoryBean">
     <property name="wsdlDocumentUrl">
         <value>http://www.xmethods.com/sd/2001/BabelFishService.wsdl</value>
     </property>
     <property name="serviceInterface">
         <value>com.springinaction.babelfish.BabelFishService</value>
     </property>
     <property name="portInterface">
         <value>com.habuma.remoting.client.BabelFishRemote</value>
     </property>
     <property name="namespaceUri">
         <value>http://www.xmethods.net/sd/BabelFishService.wsdl</value>
     </property>
     <property name="serviceName">
         <value>BabelFishService</value>
     </property>
     <property name="portName">
         <value>BabelFishPort</value>
     </property>
     <property name="serviceFactoryClass">
         <value>org.apache.axis.client.ServiceFactory</value>
     </property>
  </bean>

  public interface BabelFishService {
    public String BabelFish(String translationMode, String sourceData);
  }
  
  ApplicationContext context = new FileSystemXmlApplicationContext("babelFish.xml");
  BabelFishService babelFish = (BabelFishService) context.getBean(babelFish);
  String translated = babelFish.BabelFish("en_es", "Hello World");

11.代理 JNDI 对象。
  <bean id="sessionFactory" class="org.springframework.orm.hibernate.LocalSessionFactoryBean">
     <property name="dataSource">
        <ref bean="dataSource"/>
     </property>
     …
  </bean>
  
12.发送电子邮件。
  <bean id="mailSession" class="org.springframework.jndi.JndiObjectFactoryBean">
     <property name="jndiName">
        <value>java:comp/env/mail/Session</value>
     </property>
  </bean>
  <bean id="mailSender" class="org.springrframework.mail.javamail.JavaMailSenderImpl">
     <property name="session"><ref bean="mailSession"/></property>
  </bean>
  <bean id="enrollmentMailMessage" class="org.springframework.mail.SimpleMailMessage">
    <property name="to">
       <value>coursedirector@springtraining.com</value>
    </property>
    <property name="from">
       <value>system@springtraining.com</value>
    </property>
    <property name="subject">
       <value>Course enrollment report</value>
    </property>
  </bean>

  public class CourseServiceImpl implements CourseService {
    …
    private MailSender mailSender;
    public void setMailSender(MailSender mailSender) {
      this.mailSender = mailSender;
    }
    private SimpleMailMessage mailMessage;
    public void setMailMessage(SimpleMailMessage mailMessage) {
      this.mailMessage = mailMessage;
    }
    …
  }

  public void sendCourseEnrollmentReport() {
    Set courseList = courseDao.findAll();
    SimpleMailMessage message = new SimpleMailMessage(this.mailMessage);
    StringBuffer messageText = new StringBuffer();
    messageText.append("Current enrollment data is as follows:\n\n");
    for(Iterator iter = courseList.iterator(); iter.hasNext(); ) {
      Course course = (Course) iter.next();
      messageText.append(course.getId() + " ");
      messageText.append(course.getName() + " ");
      int enrollment = courseDao.getEnrollment(course);
      messageText.append(enrollment);
    }
    message.setText(messageText.toString());
    try {
      mailSender.send(message);
    } catch (MailException e) {
      LOGGER.error(e.getMessage());
    }
  }

  <bean id="courseService" class="com.springinaction.training.service.CourseServiceImpl">
    …
    <property name="mailMessage">
      <ref bean="enrollmentMailMessage"/>
    </property>
    <property name="mailSender">
      <ref bean="mailSender"/>
    </property>
  </bean>
  
13.使用 Java Timer 调度任务。
public class EmailReportTask extends TimerTask {
  public EmailReportTask() {}
  public void run() {
    courseService.sendCourseEnrollmentReport();
  }
  private CourseService courseService;
  public void setCourseService(CourseService courseService) {
    this.courseService = courseService;
  }
}

  <bean id="reportTimerTask" class="com.springinaction.training.schedule.EmailReportTask">
     <property name="courseService">
        <ref bean="courseService"/>
     </property>
  </bean>

  <bean id="scheduledReportTask" class="org.springframework.scheduling.timer.ScheduledTimerTask">
     <property name="timerTask">
        <ref bean="reportTimerTask"/>
     </property>
     <property name="period">
        <value>86400000</value>
     </property>
     <property name="delay">
        <value>3600000</value>
    </property>
  </bean>

14.使用 Quartz 调度器。
public class EmailReportJob extends QuartzJobBean {
  public EmailReportJob() {}
  protected void executeInternal(JobExecutionContext context) throws JobExecutionException {
    courseService.sendCourseEnrollmentReport();
  }
  private CourseService courseService;
  public void setCourseService(CourseService courseService) {
    this.courseService = courseService;
  }
}

 <bean id="reportJob" class="org.springframework.scheduling.quartz.JobDetailBean">
   <property name="jobClass">
     <value>com.springinaction.training.schedule.EmailReportJob</value>
   </property>
   <property name="jobDataAsMap">
     <map>
       <entry key="courseService">
         <ref bean="courseService"/>
       </entry>
     </map>
   </property>
 </bean>

 <bean id="simpleReportTrigger" class="org.springframework.scheduling.quartz.SimpleTriggerBean">
    <property name="jobDetail">
      <ref bean="reportJob"/>
    </property>
    <property name="startDelay">
      <value>3600000</value>
    </property>
    <property name="repeatInterval">
      <value>86400000</value>
    </property>
 </bean>

 <bean id="cronReportTrigger" class="org.springframework.scheduling.quartz.CronTriggerBean">
    <property name="jobDetail">
      <ref bean="reportJob"/>
    </property>
    <property name="cronExpression">
      <value>0 0 6 * * ?</value>
    </property>
 </bean>

 <bean class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
    <property name="triggers">
      <list>
        <ref bean="cronReportTrigger"/>
      </list>
    </property>
 </bean>

15.按调度计划调用方法。
 <bean id="scheduledReportTask" 
      class="org.springframework.scheduling.timer.MethodInvokingTimerTaskFactoryBean">
   <property name="targetObject">
     <ref bean="courseService"/>
   </property>
   <property name="targetMethod">
     <value>sendCourseEnrollmentReport</value>
   </property>
 </bean>

 <bean id="courseServiceInvokingJobDetail" 
         class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
    <property name="targetObject">
      <ref bean="courseService"/>
    </property>
    <property name="targetMethod">
      <value>sendCourseEnrollmentReport</value>
    </property>
 </bean>
```
