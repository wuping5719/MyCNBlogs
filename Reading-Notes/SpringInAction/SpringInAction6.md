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
```
