<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
6.ApplicationContext。
  (1) 利用 MessageSource 实现国际化：
  <beans>
    <bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
      <property name="basenames">
        <list>
          <value>format</value>
          <value>exceptions</value>
          <value>windows</value>
        </list>
     </property>
    </bean>
  </beans>

  (2) 事件：
  在 ApplicationContext 调用 publishEvent() 方法可以很方便的实现自定义事件。
  public class EmailBean implements ApplicationContextAware {
    private List blackList;
	  private ApplicationContext ctx;
    public void setBlackList(List blackList) {
        this.blackList = blackList;
    }
    public void setApplicationContext(ApplicationContext ctx) {
        this.ctx = ctx;
    }
    public void sendEmail(String address, String text) {
        if (blackList.contains(address)) {
            BlackListEvent event = new BlackListEvent(address, text);
            ctx.publishEvent(event);
            return;
        }
        // send email...
    }
  }

  public class BlackListNotifier implements ApplicationListener {
    private String notificationAddress;
    public void setNotificationAddress(String notificationAddress) {
        this.notificationAddress = notificationAddress;
    }
    public void onApplicationEvent(ApplicationEvent event) {
        if (event instanceof BlackListEvent) {
            // notify appropriate person...
        }
    }
  }

  (3) ApplicationContext 在 WEB 应用中的实例化:
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/daoContext.xml /WEB-INF/applicationContext.xml</param-value>
  </context-param>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  <!-- or use the ContextLoaderServlet instead of the above listener
  <servlet>
    <servlet-name>context</servlet-name>
    <servlet-class>org.springframework.web.context.ContextLoaderServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>
 -->
```
