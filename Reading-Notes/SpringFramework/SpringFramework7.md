<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
31.操作被通知对象。
   任何 AOP 代理都能够被转型为接口 org.springframework.aop.framework.Advised, 接口包括下面的方法：
   Advisor[] getAdvisors();
   void addAdvice(Advice advice) throws AopConfigException;
   void addAdvice(int pos, Advice advice) throws AopConfigException;
   void addAdvisor(Advisor advisor) throws AopConfigException;
   void addAdvisor(int pos, Advisor advisor) throws AopConfigException;
   int indexOf(Advisor advisor);
   boolean removeAdvisor(Advisor advisor) throws AopConfigException;
   void removeAdvisor(int index) throws AopConfigException;
   boolean replaceAdvisor(Advisor a, Advisor b) throws AopConfigException;
   boolean isFrozen();
  
32.自动代理 bean 定义。
   (1) BeanNameAutoProxyCreator 为名字匹配字符串或者通配符的 bean 自动创建 AOP 代理。
   <bean class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator">
      <property name="beanNames"><value>jdk*,onlyJdk</value></property>
      <property name="interceptorNames">
         <list>
            <value>myInterceptor</value>
         </list>
      </property>
   </bean>

  (2) DefaultAdvisorAutoProxyCreator 自动应用当前上下文中适当的通知器，
无需在自动代理通知器的 bean 定义中包括 bean 的名字。
  <bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator"/>
  <bean class="org.springframework.transaction.interceptor.TransactionAttributeSourceAdvisor">
     <property name="transactionInterceptor" ref="transactionInterceptor"/>
  </bean>
  <bean id="customAdvisor" class="com.mycompany.MyAdvisor"/>
  <bean id="businessObject1" class="com.mycompany.BusinessObject1"></bean>
  <bean id="businessObject2" class="com.mycompany.BusinessObject2"/>

33.使用元数据驱动的自动代理。
  <bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator"/>
  <bean class="org.springframework.transaction.interceptor.TransactionAttributeSourceAdvisor">
     <property name="transactionInterceptor" ref="transactionInterceptor"/>
  </bean>
  <bean id="transactionInterceptor" class="org.springframework.transaction.interceptor.TransactionInterceptor">
     <property name="transactionManager" ref="transactionManager"/>
     <property name="transactionAttributeSource">
        <bean class="org.springframework.transaction.interceptor.AttributesTransactionAttributeSource">
           <property name="attributes" ref="attributes"/>
        </bean>
     </property>
  </bean>    
  <bean id="attributes" class="org.springframework.metadata.commons.CommonsAttributes"/>
```
