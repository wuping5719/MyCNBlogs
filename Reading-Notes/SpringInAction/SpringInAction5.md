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
```
