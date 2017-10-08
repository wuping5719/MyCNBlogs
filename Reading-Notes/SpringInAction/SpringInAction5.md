<h2>《Spring In Action》 :books: </h2> 

> [美] Craig Walls / Ryan Breidenbach  著    人民邮电出版社

```java
34.Acegi 安全系统。
  (1) 管理身份验证: 一个认证管理器由接口 net.sf.acegisecurity.AuthenticationManager 定义：
  public interface AuthenticationManager {
    public Authentication (Authentication authentication) throws AuthenticationException;
  }

  (2) 配置 ProviderManager: ProviderManager 是认证管理器的一个实现，
它将验证身份的责任委托给一个或多个认证提供者。
  <bean id="authenticationManager" class="net.sf.acegisecurity.providers.ProviderManager">
     <property name="providers">
        <list>
           <ref bean="daoAuthenticationProvider"/>
           <ref bean="passwordDaoProvider"/>
        </list>
     </property>
  </bean>

35.根据数据库验证身份:
  <bean id="authenticationProvider" class="net.sf.acegisecurity.providers.dao.DaoAuthenticationProvider">
     <property name="authenticationDao">
        <ref bean="authenticationDao"/>
     </property>
  </bean>
  
  (1) 使用内存 DAO:
  <bean id="authenticationDao" class="net.sf.acegisecurity.providers.dao.memory.InMemoryDaoImpl">
     <property name="userMap">
        <value>
           palmerd=4moreyears,ROLE_PRESIDENT
           bauerj=ineedsleep,ROLE_FIELD_OPS,ROLE_DIRECTOR
           myersn=traitor,disabled,ROLE_CENTRAL_OPS
        </value>
     </property>
  </bean>
  
  (2) 声明一个 JDBC DAO:
  public class UsersByUsernameMapping extends MappingSqlQuery {
    protected UsersByUsernameMapping(DataSource dataSource) {
      super(dataSource, usersByUsernameQuery);
      declareParameter(new SqlParameter(Types.VARCHAR));
      compile();
    }
    protected Object mapRow(ResultSet rs, int rownum) throws SQLException {
      String username = rs.getString(1);
      String password = rs.getString(2);
      UserDetails user = new User(username, password, true, 
          new GrantedAuthority[] { new GrantedAuthorityImpl("HOLDER") });
      return user;
    }
  }

  <bean id="authenticationDao" class="net.sf.acegisecurity.providers.dao.jdbc.JdbcDaoImpl">
     <property name="dataSource">
        <ref bean="dataSource"/>
     </property>
     <property name="usersByUserNameQuery">
        <value>SELECT login, password FROM student WHERE login=?</value>
     </property>
     <property name="usersByUserNameMapping">
        <bean class="com.springinaction.training.security.UsersByUsernameMapping"/>
     </property>
     <property name="authoritiesByUserNameQuery">
        <value>SELECT login, privilege FROM user_privileges where login=?</value>
     </property>
  </bean>
  
  (3) 使用加密的密码: 设置 DaoAuthenticationProvider 的 passwordEncoder 属性改变它的密码编码器。
  PlaintextPasswordEncoder（默认）—— 不对密码进行编码，直接返回未经改变的密码；
  Md5PasswordEncoder —— 对密码进行消息摘要（MD5）编码；
  ShaPasswordEncoder —— 对密码进行安全哈希算法（SHA）编码。
  <property name="passwordEncoder">
     <bean class="net.sf.acegisecurity.providers.encoding.Md5PasswordEncoder"/>
  </property>

  (4) 设置编码器的种子源（salt source）。
  ReflectionSaltSource —— 使用用户的 User 对象中某个指定的属性来获取种子；
  SystemWideSaltSource —— 对系统中所有用户使用相同的种子。
  <property name="saltSource">
     <bean class="net.sf.acegisecurity.providers.dao.SystemWideSaltSource">
        <property name="systemWideSalt">
           <value>123XYZ</value>
        </property>
     </bean>
  </property>
  <property name="saltSource">
     <bean class="net.sf.acegisecurity.providers.dao.ReflectionSaltSource">
        <property name="userPropertyToUse">
           <value>userName</value>
        </property>
     </bean>
  </property>

  (5) 缓存用户信息:
  public interface UserCache {
     public UserDetails getUserFromCache(String username);
     public void putUserInCache(UserDetails user);
     public void removeUserFromCache(String username);
  }
  <bean id="userCache" class="net.sf.acegisecurity.providers.dao.cache.EhCacheBasedUserCache">
     <property name="minutesToIdle">15</property>
  </bean>
  <bean id="authenticationProvider" class="net.sf.acegisecurity.providers.dao.DaoAuthenticationProvider">
     ......
     <property name="userCache">
        <ref bean="userCache"/>
     </property>
  </bean>

36.根据 LDAP 仓库进行身份验证。
 <bean id="authenticationProvider" class="net.sf.acegisecurity.providers.dao.PasswordDaoAuthenticationProvider">
    <property name="passwordAuthenticationDao">
       <ref bean="passwordAuthenticationDao"/>
    </property>
 </bean>

 public interface PasswordAuthenticationDao {
    public UserDetails loadUserByUsernameAndPassword(String username,
         String password) throws DataAccessException, BadCredentialsException;
 }

 LdapPasswordAuthenticationDao 有一些用于指导它根据 LDAP 服务器进行身份验证的属性。
其中，惟一必须指定的属性是 host，它指定了 LDAP 服务器的主机名。
 <bean id="passwordAuthenticationDao" 
        class="net.sf.acegisecurity.providers.dao.ldap.LdapPasswordAuthenticationDao">
    <property name="host">
       <value>security.springinaction.com</value>
    </property>
    <property name="port">
       <value>389</value>
    </property>
    <property name="rootContext">
       <value>DC=springtraining,DC=com</value>
    </property>
    <property name="userContext">
       <value>CN=user</value>
    </property>
    <property name="rolesAttributes">
       <list>
          <value>memberOf</value>
          <value>roles</value>
       </list>
    </property>
  </bean>

37.基于 Acegi 和 Yale CAS 实现单次登录。
  (1) CAS: http://tp.its.yale.edu/tiki/tiki-index.php?page=CentralAuthenticationService
  
  <bean id="casAuthenticationProvider" class="net.sf.acegisecurity.providers.cas.CasAuthenticationProvider">
    <property name="ticketValidator">
      <ref bean="ticketValidator"/>
    </property>
    <property name="casProxyDecider">
      <ref bean="casProxyDecider"/>
    </property>
    <property name="statelessTicketCache">
      <ref bean="statelessTicketCache"/>
    </property>
    <property name="casAuthoritiesPopulator">
      <ref bean="casAuthoritiesPopulator"/>
    </property>
    <property name="key">
      <value>some_unique_key</value>
    </property>
  </bean>

  <bean id="ticketValidator" class="net.sf.acegisecurity.providers.cas.ticketvalidator.CasProxyTicketValidator">
    <property name="casValidate">
      <value>https://localhost:8443/cas/proxyValidate</value>
    </property>
    <property name="serviceProperties">
      <ref bean="serviceProperties"/>
    </property>
  </bean>

  <bean id="serviceProperties" class="net.sf.acegisecurity.ui.cas.ServiceProperties">
    <property name="service">
      <value>https://localhost:8443/training/j_acegi_cas_security_check</value>
    </property>
  </bean>

  (2) Acegi 提供了 CasProxyDecider 的三个实现类：
    AcceptAnyCasProxy —— 接受来自任何服务的代理请求；
    NamedCasProxyDecider —— 接受来自一个已命名服务的列表的代理请求；
    RejectProxyTickets —— 拒绝任何代理请求。

  <bean id="casProxyDecider" class="net.sf.acegisecurity.providers.cas.proxy.RejectProxyTickets"/>

  (3) 属性 statelessTicketCache 用于支持无状态的客户端（比如远程服务的客户端），它们无法在 HttpSession 中存储 CAS 票据。
  <bean id="statelessTicketCache" class="net.sf.acegisecurity.providers.cas.cache.EhCacheBasedTicketCache">
    <property name="minutesToIdle"><value>20</value></property>
  </bean>

  (4) Acegi 只提供了一个 CasAuthoritiesPopulator 的实现。
  DaoCasAuthoritiesPopulator 使用一个认证 DAO 从数据库中加载用户明细信息。
  <bean id="casAuthoritiesPopulator" 
     class="net.sf.acegisecurity.providers.cas.populator.DaoCasAuthoritiesPopulator">
    <property name="authenticationDao">
      <ref bean="inMemoryDaoImpl"/>
    </property>
  </bean>

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
```
