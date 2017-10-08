<h2>《Spring In Action》 :books: </h2> 

> [美] Craig Walls / Ryan Breidenbach  著    人民邮电出版社

```java
41.处理投票弃权。
  默认地，当全部投票者都投弃权票时，所有的访问决策管理者都将拒绝访问资源。
  通过设置 allowIfAllAbstain为 true，你建立了一个“沉默即同意”的策略。
  <bean id="accessDecisionManager" class="net.sf.acegisecurity.vote.UnanimousBased">
    <property name="decisionVoters">
      <list>
        <ref bean="roleVoter"/>
      </list>
    </property>
    <property name="allowIfAllAbstain">
      <value>true</value>
    </property>
  </bean>

42.代理 Acegi 的过滤器。
  要使用 FilterToBeanProxy，必须在 web.xml 文件中建立一个 <filter> 条目。
  <filter>
    <filter-name>Foo</filter-name>
    <filter-class>net.sf.acegisecurity.util.FilterToBeanProxy</filter-class>
    <init-param>
      <param-name>targetClass</param-name>
      <param-value>FooFilter</param-value>
    </init-param>
  </filter>
  在 Spring 上下文中找一个类型为 FooFilter 的 Bean，
并且把自已的过滤工作委托给在 Spring 上下文中找到的 FooFilter Bean来完成：
  <bean id="fooFilter" class="FooFilter">
     <property name="bar">
        <ref bean="bar"/>
     </property>
  </bean>
  
  /*是所有 Acegi 过滤器推荐的 URL 模式。
  <filter-mapping>
     <filter-name>Acegi-Authentication</filter-name>
     <url-pattern>/*</url-pattern>
  </filter-mapping>

43.强制 Web 安全性。
  (1) 安全强制过滤器是通过以下方式在 Spring 配置文件中声明的：
  <bean id="securityEnforcementFilter" class="net.sf.acegisecurity.intercept.web.SecurityEnforcementFilter">
    <property name="securityInterceptor">
       <ref bean="securityInterceptor"/>
    </property>
    <property name="authenticationEntryPoint">
      <ref bean="authenticationEntryPoint"/>
    </property>
  </bean>

  (2) 使用一个过滤器安全拦截器:
  Acegi 的 FilterSecurityInterceptor 类负责执行安全拦截器的工作。
  <bean id="securityInterceptor" class="net.sf.acegisecurity.intercept.web.FilterSecurityInterceptor">
    <property name="authenticationManager">
      <ref bean="authenticationManager"/>
    </property>
    <property name="accessDecisionManager">
      <ref bean="accessDecisionManager"/>
    </property>
    <property name="objectDefinitionSource">
      <value>
        CONVERT_URL_TO_LOWERCASE_BEFORE_COMPARISON
        \A/admin/.*\Z=ROLE_ADMIN
        \A/student/.*\Z=ROLE_STUDENT,ROLE_ALUMNI
        \A/instruct/.*\Z=ROLE_INSTRUCTOR
      </value>
    </property>
  </bean>

  (3) securityInterceptor Bean 的 objectDefinitionSource 属性定义了：
    /admin/reports.htm 要求用户拥有 ROLE_ADMIN 权限；
    /student/manageSchedule.htm 要求用户拥有 ROLE_STUDENT 或 ROLE_ALUMNI 权限；
    /instruct/postCourseNotes.htm 要求用户拥有 ROLE_INSTUCTOR 权限。

  (4) 以下的 objectDefinitionSource 属性定义与上面给出的那个定义是等价的：
  <property name="objectDefinitionSource">
    <value>
      CONVERT_URL_TO_LOWERCASE_BEFORE_COMPARISON
      PATTERN_TYPE_APACHE_ANT
      /admin/**=ROLE_ADMIN
      /student/**=ROLE_STUDENT,ROLE_ALUMNI
      /instruct/**=ROLE_INSTRUCTOR
    </value>
  </property>

  (5) 配置一个安全强制过滤器的第一步是在应用的 web.xml 文件中为 FilterToBeanProxy 
增加 <filter> 和 <filter-mapping> 元素：
 <filter>
    <filter-name>Acegi-Security</filter-name>
    <filter-class>net.sf.acegisecurity.util.FilterToBeanProxy</filter-class>
    <init-param>
      <param-name>targetBean</param-name>
      <param-value>securityEnforcementFilter</param-value>
    </init-param>
 </filter>
 …
 <filter-mapping>
  <filter-name>Acegi-Security</filter-name>
  <url-pattern>/*</url-pattern>
 </filter-mapping>

44.处理登录。
  (1) 认证入口点的主要目的是提示用户登录。Acegi 提供了三个现成的认证入口点：
   BasicProcessingFilterEntryPoint —— 通过向浏览器发送一个 HTTP 401（未授权）消息，
由浏览器弹出登录对话框，提示用户登录；
   AuthenticationProcessingFilterEntryPoint —— 将用户重定向到一个基于 HTML 表单的登录页面；
   CasProcessingFilterEntryPoint —— 将用户重定向至一个 Yale CAS 登录页面。
   
  (2) 处理认证请求的工作由认证处理过滤器负责完成。Acegi 提供了三个认证处理过滤器：
   BasicProcessingFilter —— 处理 HTTP 基本身份验证请求；
   AuthenticationProcessingFilter —— 处理基于表单的身份验证请求；
   CasProcessingFilter —— 基于CAS服务票据的存在性和有效性验证用户身份。

  (3) 基本身份验证:
  <bean id="authenticationEntryPoint" 
      class="net.sf.acegisecurity.ui.basicauth.BasicProcessingFilterEntryPoint">
    <property name="realmName">
       <value>Spring Training</value>
    </property>
  </bean>

  BasicProcessFilter 取得用户名和密码并进行处理。
  <bean id="basicProcessingFilter" class="net.sf.acegisecurity.ui.basicauth.BasicProcessingFilter">
    <property name="authenticationManager">
      <ref bean="authenticationManager"/>
    </property>
    <property name="authenticationEntryPoint">
      <ref bean="authenticationEntryPoint"/>
    </property>
  </bean>

  BasicProcessingFilter 需要一个在 web.xml 中配置的 FilterToBeanProxy：
  <filter>
    <filter-name>Acegi-Authentication</filter-name>
    <filter-class>net.sf.acegisecurity.util.FilterToBeanProxy</filter-class>
    <init-param>
      <param-name>targetBean</param-name>
      <param-value>net.sf.acegisecurity.ui.basicauth.BasicProcessingFilter</param-value>
    </init-param>
  </filter>
  …
  <filter-mapping>
    <filter-name>Acegi-Authentication</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

  (4) 基于表单的身份验证:
  Acegi 的 AuthenticationProcessingFilterEntryPoint 是一个提供给用户基于 HTML 的登录表单的认证入口点。
  <bean id="authenticationEntryPoint" 
      class="net.sf.acegisecurity.ui.webapp.AuthenticationProcessingFilterEntryPoint">
    <property name="loginFormUrl">
      <value>/jsp/login.jsp</value>
    </property>
    <property name="forceHttps"><value>true</value></property>
  </bean>

  login.jsp
  <form method="POST" action="j_acegi_security_check">
    <input type="text" name="j_username"><br>
    <input type="password" name="j_password"><br>
    <input type="submit">
  </form>

  AuthenticationProcessingFilter 是处理基于表单身份验证的过滤器。
  <bean id="authenticationProcessingFilter" 
      class="net.sf.acegisecurity.ui.webapp.AuthenticationProcessingFilter">
    <property name="filterProcessesUrl">
      <value>/j_acegi_security_check</value>
    </property>
    <property name="authenticationFailureUrl">
      <value>/jsp/login.jsp?failed=true</value>
    </property>
    <property name="defaultTargetUrl">
      <value>/</value>
    </property>
    <property name="authenticationManager">
      <ref bean="authenticationManager"/>
    </property>
  </bean>

  配置一个 FilterToBeanProxy，它能够将过滤处理委托给 authenticationProcessingFilter Bean：
  <filter>
    <filter-name>Acegi-Authentication</filter-name>
    <filter-class>net.sf.acegisecurity.util.FilterToBeanProxy</filter-class>
    <init-param>
      <param-name>targetClass</param-name>
      <param-value>net.sf.acegisecurity.ui.webapp.AuthenticationProcessingFilter</param-value>
    </init-param>
  </filter>
  …
  <filter-mapping>
    <filter-name>Acegi-Authentication</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>

  (5) CAS 身份验证:
  Acegi 的 CasProcessingFilterEntryPoint 是一个能够将用户送到 CAS 服务器进行登录的认证入口点。
  <bean id="authenticationEntryPoint" class="net.sf.acegisecurity.ui.cas.CasProcessingFilterEntryPoint">
    <property name="loginUrl">
      <value>https://localhost:8443/cas/login</value>
    </property>
    <property name="serviceProperties">
      <ref bean="serviceProperties"/>
    </property>
  </bean>

  CasProcessingFilter 是一个认证处理过滤器，它会拦截来自 CAS 服务器的包含待验证票据的请求。
  <bean id="authenticationProcessingFilter" class="net.sf.acegisecurity.ui.cas.CasProcessingFilter">
    <property name="filterProcessesUrl">
      <value>/j_acegi_cas_security_check</value>
    </property>
    <property name="authenticationManager">
      <ref bean="authenticationManager"/>
    </property>
    <property name="authenticationFailureUrl">
      <value>/authenticationfailed.jsp</value>
    </property>
    <property name="defaultTargetUrl">
      <value>/</value>
    </property>
  </bean>

45.设置一个安全上下文。
  (1) Acegi 提供了若干个集成过滤器，其中 HttpSessionIntegrationFilter 适用于大多数情形。
  <bean id="integrationFilter" class="net.sf.acegisecurity.ui.webapp.HttpSessionIntegrationFilter"/>

  (2) 在 web.xml 中配置一个 FilterToBeanProxy 过滤器:
  <filter>
    <filter-name>Acegi-Integration</filter-name>
    <filter-class>net.sf.acegisecurity.util.FilterToBeanProxy</filter-class>
    <init-param>
      <param-name>targetClass</param-name>
      <param-value>net.sf.acegisecurity.ui.AutoIntegrationFilter</param-value>
    </init-param>
  </filter>
  …
  <filter-mapping>
    <filter-name>Acegi-Integration</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
  要把集成过滤器的 <filter-mapping> 配置项放到所有其他 Acegi 过滤器的 <filter-mapping> 的后面。

46.确保通道安全性。
  (1) 在 Web 应用的 web.xml 文件中再增加一个 FilterToBeanProxy 配置：
  <filter>
    <filter-name>Acegi-Channel</filter-name>
    <filter-class>net.sf.acegisecurity.util.FilterToBeanProxy</filter-class>
    <init-param>
      <param-name>targetClass</param-name>
      <param-value>
        net.sf.acegisecurity.securechannel.ChannelProcessingFilter
      </param-value>
    </init-param>
  </filter>
  …
  <filter-mapping>
    <filter-name>Acegi-Channel</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
  ChannelProcessingFilter 的 <filter-mapping> 定义必须出现在任何其他的 <filter-mapping> 之前。

  (2) 在 Spring 配置文件中声明受委托的过滤器 Bean：
  <bean id="channelProcessingFilter" class="net.sf.acegisecurity.securechannel.ChannelProcessingFilter">
    <property name="filterInvocationDefinitionSource">
      <value>
        CONVERT_URL_TO_LOWERCASE_BEFORE_COMPARISON
        \A/secure/.*\Z=REQUIRES_SECURE_CHANNEL
        \A/login.jsp.*\Z=REQUIRES_SECURE_CHANNEL
        \A/j_acegi_security_check.*\Z=REQUIRES_SECURE_CHANNEL
        \A.*\Z=REQUIRES_INSECURE_CHANNEL
      </value>
    </property>
    <property name="channelDecisionManager">
      <ref bean="channelDecisionManager"/>
    </property>
  </bean>

  (3) 在 URL 模式之上可以应用两种通道规则：
   REQUIRES_SECURE_CHANNEL —— 规定与该模式匹配的 URL 必须经过一个安全通道传输（例如，HTTPS）；
   REQUIRES_INSECURE_CHANNEL —— 规定与该模式匹配的 URL 必须经过一个非安全的通道传输（例如，HTTP）。

  (4) 通道决策管理器 ChannelDecisionManagerImpl 轮询它的所有通道处理器，给予他们重载请求通道的机会。
  <bean id="channelDecisionManager" 
        class="net.sf.acegisecurity.securechannel.ChannelDecisionManagerImpl">
   <property name="channelProcessors">
     <list>
       <ref bean="secureChannelProcessor"/>
       <ref bean="insecureChannelProcessor"/>
     </list>
   </property>
 </bean>

 (5) ChannelDecisionManagerImpl 的通道处理器是通过 channelProcessors 属性提供的。
 <bean id="secureChannelProcessor" class="net.sf.acegisecurity.securechannel.SecureChannelProcessor"/>
 <bean id="insecureChannelProcessor" class="net.sf.acegisecurity.securechannel.InsecureChannelProcessor"/>

47.使用 Acegi 的标签库。
  <authz:authorize> 标签能够根据当前用户是否拥有恰当权限来决定显示或隐藏 Web 页面的内容。
  <authz:authorize> 是一个流程控制标签，能够在满足特定安全需求的条件下显示它的内容体。它有三个互斥的参数：
    ifAllGranted —— 是一个由逗号分隔的权限列表，用户必须拥有所有列出的权限才能渲染标签体；
    ifAnyGranted —— 是一个由逗号分隔的权限列表，用户必须至少拥有其中的一个才能渲染标签体；
    ifNotGranted —— 是一个由逗号分隔的权限列表，用户必须不拥有其中的任何一个才能渲染标签体。
    
  使用 <authz:authorize> 标签，在用户没有管理员权限的情况下，可以避免渲染到课程编辑页面的链接：
  <authz:authorize ifAllGranted="ROLE_ADMINISTRATOR">
    <a href="admin/editCourse.htm?courseId=${course.id}">Edit Course</a>
  </authz:authorize>

48.创建一个安全切面。
  (1) 设置一个 AOP 代理的最简单的方式是使用 Spring 的 BeanNameAutoProxyCreator。
  <bean id="autoProxyCreator" class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator">
    <property name="interceptorNames">
      <list>
        <value>securityInterceptor</value>
      </list>
    </property>
    <property name="beanNames">
      <list>
        <value>courseService</value>
        <value>billingService</value>
      </list>
    </property>
  </bean>

  (2) 自动代理创建器 autoProxyCreator 通过一个名为 securityInterceptor 拦截器代理它的 Bean。
  <bean id="securityInterceptor" class="net.sf.acegisecurity.intercept.method.MethodSecurityInterceptor">
    <property name="authenticationManager">
      <ref bean="authenticationManager"/>
    </property>
    <property name="accessDecisionManager">
      <ref bean="accessDecisionManager"/>
    </property>
    <property name="objectDefinitionSource">
      <value>
        com.springinaction.springtraining.service.CourseService.createCourse=ROLE_ADMIN
        com.springinaction.springtraining.service.CourseService.enroll*=ROLE_ADMIN,ROLE_REGISTRAR
      </value>
    </property>
  </bean>

49.使用元数据保护方法。
  (1) 声明一个元数据实现以告诉 Spring 如何装载元数据。
  <bean id="attributes" class="org.springframework.metadata.commons.CommonsAttributes"/>

  (2) Acegi 的 MethodDefinitionAttributes 是一个对象定义源，它能够从受保护对象的元数据中获取它的安全属性：
  <bean id="objectDefinitionSource" 
       class="net.sf.acegisecurity.intercept. method.MethodDefinitionAttributes">
    <property name="attributes"><ref bean="attributes"/></property>
  </bean>

  (3) 把 objectDefinitionSource 装配到 MethodSecurityInterceptor 的 objectDefinitionSource 属性中。
  <bean id="securityInterceptor" class="net.sf.acegisecurity.intercept.method.MethodSecurityInterceptor">
  …
    <property name="objectDefinitionSource">
      <ref bean="objectDefinitionSource"/>
    </property>
  </bean>

  (4) 标记 CourseService 的 enrollStudentInCourse() 方法，声明它需要 ROLE_ADMIN 或 ROLE_REGISTRAR 属性：
  /**
   * @@net.sf.acegisecurity.SecurityConfig("ROLE_ADMIN")
   * @@net.sf.acegisecurity.SecurityConfig("ROLE_REGISTRAR")
   */
  public void enrollStudentInCourse(Course course,Student student) throws CourseException;
```
