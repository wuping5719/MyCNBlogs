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
```
