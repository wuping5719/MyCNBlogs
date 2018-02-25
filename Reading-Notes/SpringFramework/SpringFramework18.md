<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
96.Spring 邮件抽象层。
   Spring 邮件抽象层的主要包为 org.springframework.mail。它包括了发送电子邮件的主要接口 MailSender
和值对象 SimpleMailMessage，它封装了简单邮件的属性如 from, to, cc, subject, text。

97.使用 Spring 邮件抽象。
   (1) MailSender 和 SimpleMailMessage 的基本用法。
   <bean id="mailSender" class="org.springframework.mail.javamail.JavaMailSenderImpl">
      <property name="host" value="mail.ouc.com"/>
   </bean>
   <bean id="templateMessage" class="org.springframework.mail.SimpleMailMessage">
      <property name="from" value="customerservice@ouc.com"/>
      <property name="subject" value="Your order"/>
   </bean>
   <bean id="orderManager" class="com.ouc.businessapp.support.SimpleOrderManager">
      <property name="mailSender" ref="mailSender"/>
      <property name="templateMessage" ref="templateMessage"/>
   </bean>

   (2) 使用 JavaMailSender 和 MimeMessagePreparator。
   import javax.mail.Message;
   import javax.mail.MessagingException;
   import javax.mail.internet.InternetAddress;
   import javax.mail.internet.MimeMessage;
   import javax.mail.internet.MimeMessage;
   import org.springframework.mail.MailException;
   import org.springframework.mail.javamail.JavaMailSender;
   import org.springframework.mail.javamail.MimeMessagePreparator;
   public class SimpleOrderManager implements OrderManager {
      private JavaMailSender mailSender;
      public void setMailSender(JavaMailSender mailSender) {
         this.mailSender = mailSender;
      }
      public void placeOrder(final Order order) {
         MimeMessagePreparator preparator = new MimeMessagePreparator() {
            public void prepare(MimeMessage mimeMessage) throws Exception {
                mimeMessage.setRecipient(Message.RecipientType.TO,
                        new InternetAddress(order.getCustomer().getEmailAddress()));
                mimeMessage.setFrom(new InternetAddress("mail@ouc.com"));
                mimeMessage.setText("Dear " + order.getCustomer().getFirstName() + " "
                        + order.getCustomer().getLastName()
                        + ", thank you for placing order. Your order number is "
                        + order.getOrderNumber());
            }
         };
         try {
            this.mailSender.send(preparator);
         }
         catch (MailException ex) {
            System.err.println(ex.getMessage());
         }
      }
   }

98.使用 MimeMessageHelper。
   org.springframework.mail.javamail.MimeMessageHelper 是处理 JavaMail 邮件时比较顺手组件之一。
   (1) 发送附件和嵌入式资源(inline resources)。
   JavaMailSenderImpl sender = new JavaMailSenderImpl();
   sender.setHost("mail.host.com");
   MimeMessage message = sender.createMimeMessage();
   // use the true flag to indicate you need a multipart message
   MimeMessageHelper helper = new MimeMessageHelper(message, true);
   helper.setTo("test@host.com");
   helper.setText("Check out this image!");
   // let's attach the infamous windows Sample file (this time copied to c:/)
   FileSystemResource file = new FileSystemResource(new File("c:/Sample.jpg"));
   helper.addAttachment("CoolImage.jpg", file);
   // helper.addInline("identifier1234", file);
   sender.send(message);

   (2) 使用模板来创建邮件内容。

99.使用 OpenSymphony Quartz 调度器。
   (1) 使用 JobDetailBean。
   JobDetail 对象保存运行一个任务所需的全部信息。Spring 提供一个叫作 JobDetailBean 的类让 JobDetail 
能对一些有意义的初始值进行初始化。
   (2) 使用 MethodInvokingJobDetailFactoryBean。
   <bean id="jobDetail" class="org.springframework.scheduling.quartz.MethodInvokingJobDetailFactoryBean">
      <property name="targetObject" ref="exampleBusinessObject" />
      <property name="targetMethod" value="doIt" />
   </bean>
   (3) 使用 triggers 和 SchedulerFactoryBean 来包装任务。
   Spring 提供两个子类 triggers，分别为 CronTriggerBean 和 SimpleTriggerBean。
```
