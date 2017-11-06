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

34.热交换目标源。
  org.springframework.aop.target.HotSwappableTargetSource 允许当调用者保持引用的时候，切换一个 AOP 代理的目标。 

  HotSwappableTargetSource swapper = (HotSwappableTargetSource) beanFactory.getBean("swapper");
  Object oldTarget = swapper.swap(newTarget);

  <bean id="initialTarget" class="mycompany.OldTarget"/>
  <bean id="swapper" class="org.springframework.aop.target.HotSwappableTargetSource">
     <constructor-arg ref="initialTarget"/>
  </bean>
  <bean id="swappable" class="org.springframework.aop.framework.ProxyFactoryBean">
     <property name="targetSource" ref="swapper"/>
  </bean>

35.池化目标源。
  Spring 对 Jakarta Commons Pool 1.3 提供了开箱即用的支持，后者提供了一个相当有效的池化实现。
  
  <bean id="businessObjectTarget" class="com.mycompany.MyBusinessObject" scope="prototype"></bean>
  <bean id="poolTargetSource" class="org.springframework.aop.target.CommonsPoolTargetSource">
     <property name="targetBeanName" value="businessObjectTarget"/>
     <property name="maxSize" value="25"/>
  </bean>
  <bean id="businessObject" class="org.springframework.aop.framework.ProxyFactoryBean">
     <property name="targetSource" ref="poolTargetSource"/>
     <property name="interceptorNames" value="myInterceptor"/>
  </bean>
  
  可以配置 Spring 来把任何被池化对象转型到 org.springframework.aop.target.PoolingConfig 接口。
  <bean id="poolConfigAdvisor" class="org.springframework.beans.factory.config.MethodInvokingFactoryBean">
     <property name="targetObject" ref="poolTargetSource"/>
     <property name="targetMethod" value="getPoolingConfigMixin"/>
  </bean>

36.原型目标源。
  <bean id="prototypeTargetSource" class="org.springframework.aop.target.PrototypeTargetSource">
     <property name="targetBeanName" ref="businessObjectTarget"/>
  </bean>

37.ThreadLocal 目标源。
  <bean id="threadlocalTargetSource" class="org.springframework.aop.target.ThreadLocalTargetSource">
     <property name="targetBeanName" value="businessObjectTarget"/>
  </bean>
```
