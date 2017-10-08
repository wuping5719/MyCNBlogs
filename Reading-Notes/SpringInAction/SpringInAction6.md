<h2>《Spring In Action》 :books: </h2> 

> [美] Craig Walls / Ryan Breidenbach  著    人民邮电出版社

```java
38.控制访问。
  一个访问决策管理器是由 net.sf.acegisecurity.AccessDecisionManager 接口定义的：
  public interface AccessDecisionManager {
     // decide() 方法是完成最终决策的地方
     public void decide(Authentication authentication, Object object,
         ConfigAttributeDefinition config) throws AccessDeniedException;
     // supports() 方法根据受保护资源的类以及它的配置属性（受保护资源的访问需求）
     // 判断该访问管理器是否能够做出针对该资源的访问决策。
     public boolean supports(ConfigAttribute attribute);
     public boolean supports(Class clazz);
  }

39.访问决策投票。
  AccessDecisionManager 的三个实现类：
    net.sf.acegisecurity.vote.AffirmativeBased —— 当至少有一个投票者投允许访问票时允许访问
    net.sf.acegisecurity.vote.ConsensusBased —— 当所有投票者都投允许访问票时允许访问
    net.sf.acegisecurity.vote.UnanimousBased —— 当没有投票者投拒绝访问票时允许访问

  配置了一个 UnanimousBased 访问决策管理器：
  <bean id="accessDecisionManager" class="net.sf.acegisecurity.vote.UnanimousBased">
    <property name="decisionVoters">
       <list>
          <ref bean="roleVoter"/>
       </list>
    </property>
  </bean>

40.决定如何投票。
  一个访问决策投票者是任何实现了 net.sf.acegisecurity.vote.AccessDecisionVoter 接口的对象：
  public interface AccessDecisionVoter {
    public static final int ACCESS_GRANTED = 1;   // 投票者希望允许访问受保护的资源
    public static final int ACCESS_ABSTAIN = 0;   // 投票者不关心
    public static final int ACCESS_DENIED = -1;   // 投票者希望拒绝对受保护资源的访问
    public boolean supports(ConfigAttribute attribute);
    public boolean supports(Class clazz);
    public int vote(Authentication authentication, Object object, ConfigAttributeDefinition config);
  }

  Acegi 提供了一个很实用的投票者实现类 RoleVoter：
  <bean id="roleVoter" class="net.sf.acegisecurity.vote.RoleVoter">
    <property name="rolePrefix">
      <value>GROUP_</value>
    </property>
  </bean>

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
```
