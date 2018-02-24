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
```
