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

7.基于注解（Annotation-based）的配置。
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:context="http://www.springframework.org/schema/context"
     xsi:schemaLocation="http://www.springframework.org/schema/beans 
         http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context-2.5.xsd">       
     <context:annotation-config/>
  </beans>

  (1) @Autowired：
  public class MovieRecommender {
     @Autowired
     private MovieCatalog movieCatalog;

     private CustomerPreferenceDao customerPreferenceDao;

     @Autowired
     public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
        this.customerPreferenceDao = customerPreferenceDao;
     }
  }

  (2) 基于注解的自动连接微调:
  @Target({ElementType.FIELD, ElementType.PARAMETER})
  @Retention(RetentionPolicy.RUNTIME)
  @Qualifier
  public @interface Genre {
    String value();
  }

  public class MovieRecommender {
    @Autowired
    @Genre("Action")
    private MovieCatalog actionCatalog;
    private MovieCatalog comedyCatalog;
    @Autowired
    public void setComedyCatalog(@Genre("Comedy") MovieCatalog comedyCatalog) {
        this.comedyCatalog = comedyCatalog;
    }
  }

  (3) CustomAutowireConfigurer：
  <bean id="customAutowireConfigurer" 
      class="org.springframework.beans.factory.annotation.CustomAutowireConfigurer">
     <property name="customQualifierTypes">
        <set>
            <value>example.CustomQualifier</value>
        </set>
     </property>
  </bean>

  (4) @Resource：
  public class SimpleMovieLister {
    private MovieFinder movieFinder;
    @Resource(name="myMovieFinder")
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
  }

  (5) @PostConstruct 与 @PreDestroy：
  public class CachingMovieLister {
    @PostConstruct
    public void populateMovieCache() {
      // populates the movie cache upon initialization...
    }
    @PreDestroy
    public void clearMovieCache() {
      // clears the movie cache upon destruction...
    }
  }

8.对受管组件的 Classpath 扫描。
```
