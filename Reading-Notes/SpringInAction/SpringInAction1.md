<h2>《Spring In Action》 :books: </h2> 

> [美] Craig Walls / Ryan Breidenbach  著    人民邮电出版社

```java
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
 
16.使用 JMS 模板发送消息。
 private JmsTemplate jmsTemplate;
 public void setJmsTemplate(JmsTemplate jmsTemplate) {
   this.jmsTemplate = jmsTemplate;
 }

 public void sendSettlementMessage(final PaySettlement settlement) {
   jmsTemplate.send(
     new MessageCreator() {
       public Message createMessage(Session session) throws JMSException {
           MapMessage message = session.createMapMessage();
           message.setString("authCode", settlement.getAuthCode());
           message.setString("customerName", settlement.getCustomerName());
           message.setString("creditCardNumber", settlement.getCreditCardNumber());
           message.setInt("expirationMonth", settlement.getExpirationMonth());
           message.setInt("expirationYear", settlement.getExpirationYear());
           return message;
         }
       }
   );
 }

 <bean id="paymentService" class="com.springinaction.training.service.PaymentServiceImpl">
    …
    <property name="jmsTemplate">
      <ref bean="jmsTemplate"/>
    </property>
 <bean>
 <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
    <property name="connectionFactory">
      <ref bean="jmsConnectionFactory"/>
    </property>
    <property name="defaultDestination">
      <ref bean="destination"/>
    </property>
 </bean>
 <bean id="jmsConnectionFactory" class="org.springframework.jndi.JndiObjectFactoryBean">
    <property name="jndiName">
      <value>connectionFactory</value>
    </property>
 </bean>
 <bean id="destination" class="org.springframework.jndi.JndiObjectFactoryBean">
    <property name="jndiName">
      <value>creditCardQueue</value>
    </property>
 </bean>

 jmsTemplate.send("creditCardQueue", new MessageCreator() { … });

 <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
   …
   <property name="pubSubDomain">
     <value>true</value>
   </property>
 </bean>

17.消费消息。
public PaySettlement processSettlementMessages() {
  Message msg = jmsTemplate.receive("creditCardQueue");
  try {
    MapMessage mapMessage = (MapMessage) msg;
    PaySettlement paySettlement = new PaySettlement();
    paySettlement.setAuthCode(mapMessage.getString("authCode"));
    paySettlement.setCreditCardNumber(mapMessage.getString("creditCardNumber"));
    paySettlement.setCustomerName(mapMessage.getString("customerName"));
    paySettlement.setExpirationMonth(mapMessage.getInt("expirationMonth"));
    paySettlement.setExpirationYear(mapMessage.getInt("expirationYear"));
    return paySettlement;
  } catch (JMSException e) {
    throw JmsUtils.convertJmsAccessException(e);
  }
}

<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
  <property name="receiveTimeout">
      <value>10000</value>
  </property>
</bean>

18.转换消息。
public class PaySettlementConverter implements MessageConverter {
   public PaySettlementConverter() {}

   public Object fromMessage(Message message) throws MessageConversionException {
     MapMessage mapMessage = (MapMessage) message;
     PaySettlement settlement = new PaySettlement();
     try {
       settlement.setAuthCode(mapMessage.getString("authCode"));
       settlement.setCreditCardNumber(mapMessage.getString("creditCardNumber"));
       settlement.setCustomerName(mapMessage.getString("customerName"));
       settlement.setExpirationMonth(mapMessage.getInt("expirationMonth"));
       settlement.setExpirationYear(mapMessage.getInt("expirationYear"));
     } catch (JMSException e) {
       throw new MessageConversionException(e.getMessage());
     }
     return settlement;
   }

   public Message toMessage(Object object, Session session) throws JMSException,
        MessageConversionException {
     PaySettlement settlement = (PaySettlement) object;
     MapMessage message = session.createMapMessage();
     message.setString("authCode", settlement.getAuthCode());
     message.setString("customerName", settlement.getCustomerName());
     message.setString("creditCardNumber", settlement.getCreditCardNumber());
     message.setInt("expirationMonth", settlement.getExpirationMonth());
     message.setInt("expirationYear", settlement.getExpirationYear());
     return message;
   }
 }

 <bean id="settlementConverter" class="com.springinaction.training.service.PaySettlementConverter">
    …
 </bean>

 <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
    …
    <property name="messageConverter">
      <ref bean="settlementConverter"/>
    </property>
 </bean>

 public void sendSettlementMessage(PaySettlement settlement) {
     jmsTemplate.convertAndSend(settlement);
 }
 PaySettlement settlement = (PaySettlement) jmsTemplate.receiveAndConvert("creditCardQueue");
 
19.定义 Velocity 视图: http://jakarta.apache.org/velocity.
  基于 Velocity 的课程列表: courseList.vm
  <html>
    <head>
      <title>Course List</title>
    </head>
    <body>
      <h2>COURSE LIST</h2>
      <table width="600" border="1" cellspacing="1" cellpadding="1">
        <tr bgcolor="#999999">
          <td>Course ID</td>
          <td>Name</td>
          <td>Instructor</td>
          <td>Start</td>
          <td>End</td>
        </tr>
        #foreach($course in $courses)
        <tr>
          <td><a href="displayCourse.htm?id=${course.id}"> ${course.id} </a></td>
          <td> ${course.name} </td>
          <td> ${course.instructor.lastName} </td>
          <td> ${course.startDate} </td>
          <td> ${course.endDate} </td>
        </tr>
        #end  // 在所有课程中循环
      </table>
    </body>
  </html>
```
