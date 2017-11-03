<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
16.@AspectJ 支持。
  (1) 启用 @AspectJ 支持：
    <aop:aspectj-autoproxy />
    <bean class="org.springframework.aop.aspectj.annotation.AnnotationAwareAspectJAutoProxyCreator" />
  (2) 声明一个切面：
    <bean id="myAspect" class="org.xyz.NotVeryUsefulAspect">
       <!-- configure properties of aspect here as normal -->
    </bean>
    
    package org.xyz;
    import org.aspectj.lang.annotation.Aspect;
    @Aspect
    public class NotVeryUsefulAspect { }
  (3) 声明一个切入点 (pointcut)：
    @Pointcut("execution(public * *(..))")
    private void anyPublicOperation() { }
    @Pointcut("within(com.xyz.someapp.trading..*)")
    private void inTrading() {}
    @Pointcut("anyPublicOperation() && inTrading()")
    private void tradingOperation() {}
    
17.声明通知。
  (1) 前置通知：
  import org.aspectj.lang.annotation.Aspect;
  import org.aspectj.lang.annotation.Before;
  @Aspect
  public class BeforeExample {
     @Before("execution(* com.xyz.myapp.dao.*.*(..))")
     public void doAccessCheck() { }
  }

  (2) 后置通知：
  import org.aspectj.lang.annotation.Aspect;
  import org.aspectj.lang.annotation.AfterReturning;
  @Aspect
  public class AfterReturningExample {
     @AfterReturning(pointcut="com.xyz.myapp.SystemArchitecture.dataAccessOperation()", returning="retVal")
     public void doAccessCheck(Object retVal) { }
  }

  (3) 异常通知：
  import org.aspectj.lang.annotation.Aspect;
  import org.aspectj.lang.annotation.AfterThrowing;
  @Aspect
  public class AfterThrowingExample {
     @AfterThrowing(pointcut="com.xyz.myapp.SystemArchitecture.dataAccessOperation()", throwing="ex")
     public void doRecoveryActions(DataAccessException ex) { }
  }

  (4) 最终通知：
  import org.aspectj.lang.annotation.Aspect;
  import org.aspectj.lang.annotation.After;
  @Aspect
  public class AfterFinallyExample {
     @After("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
     public void doReleaseLock() { }
  }

  (5) 环绕通知：
   环绕通知使用 @Around 注解来声明。通知的第一个参数必须是 ProceedingJoinPoint 类型。
  import org.aspectj.lang.annotation.Aspect;
  import org.aspectj.lang.annotation.Around;
  import org.aspectj.lang.ProceedingJoinPoint;
  @Aspect
  public class AroundExample {
    @Around("com.xyz.myapp.SystemArchitecture.businessService()")
    public Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable {
      // start stopwatch
      Object retVal = pjp.proceed();
      // stop stopwatch
      return retVal;
    }
  }

18.引入(Introduction)。
 使用 @DeclareParents 注解来定义引入。
 @Aspect
 public class UsageTracking {
   @DeclareParents(value="com.xzy.myapp.service.*+", defaultImpl=DefaultUsageTracked.class)
   public static UsageTracked mixin;
   
   @Before("com.xyz.myapp.SystemArchitecture.businessService() &&" + "this(usageTracked)")
   public void recordUsage(UsageTracked usageTracked) {
      usageTracked.incrementUseCount();
   }
 }

 UsageTracked usageTracked = (UsageTracked) context.getBean("myService");

19.切面实例化模型。
  AspectJ 的 perthis 和 pertarget 实例化模型。
  @Aspect("perthis(com.xyz.myapp.SystemArchitecture.businessService())")
  public class MyAspect {
     private int someState;

     @Before(com.xyz.myapp.SystemArchitecture.businessService())
     public void recordServiceUsage() { }
  }

20.基于 Schema 的 AOP 支持。
  (1) 声明一个切面:
  <aop:config>
     <aop:aspect id="myAspect" ref="aBean"> ... </aop:aspect>
  </aop:config>

  <bean id="aBean" class="..."> ... </bean>

  (2) 声明一个切入点:
  <aop:config>
     <aop:pointcut id="businessService" expression="execution(* com.xyz.myapp.service.*.*(..))" />
  </aop:config>

  (3) 声明通知: 前置通知.
  <aop:aspect id="beforeExample" ref="aBean">
     <aop:before pointcut="execution(* com.xyz.myapp.dao.*.*(..))" method="doAccessCheck" />      
     ...
  </aop:aspect>

  (4) 声明通知: 后置通知.
  <aop:aspect id="afterReturningExample" ref="aBean">
     <aop:after-returning pointcut-ref="dataAccessOperation" returning="retVal" method="doAccessCheck"/>  
     ...
  </aop:aspect>
 
  (5) 引入:
  <aop:aspect id="usageTrackerAspect" ref="usageTracking">
    <aop:declare-parents types-matching="com.xzy.myapp.service.*+" 
       implement-interface="com.xyz.myapp.service.tracking.UsageTracked"
       default-impl="com.xyz.myapp.service.tracking.DefaultUsageTracked" />
       
    <aop:before pointcut="com.xyz.myapp.SystemArchitecture.businessService() and this(usageTracked)"
       method="recordUsage"/>
  </aop:aspect>

  (6) Advisor:
  <aop:config>
    <aop:pointcut id="businessService" expression="execution(* com.xyz.myapp.service.*.*(..))"/>
    <aop:advisor pointcut-ref="businessService" advice-ref="tx-advice"/>  
  </aop:config>

  <tx:advice id="tx-advice">
    <tx:attributes>
       <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
  </tx:advice>
  
21.代理机制。
  <aop:config proxy-target-class="true">
     <!-- other beans defined here... -->
  </aop:config>

  <aop:aspectj-autoproxy proxy-target-class="true"/>

  public class Main {
    public static void main(String[] args) {
       ProxyFactory factory = new ProxyFactory(new SimplePojo());
       factory.addInterface(Pojo.class);
       factory.addAdvice(new RetryAdvice());
       factory.setExposeProxy(true);
       
       Pojo pojo = (Pojo) factory.getProxy();
       // this is a method call on the proxy!
       pojo.foo();
    }
  }

22.以编程方式创建 @AspectJ 代理。
   // create a factory that can generate a proxy for the given target object
   AspectJProxyFactory factory = new AspectJProxyFactory(targetObject); 
   // add an aspect, the class must be an @AspectJ aspect
   // you can call this as many times as you need with different aspects
   factory.addAspect(SecurityManager.class);
   // you can also add existing aspect instances, the type of the object supplied must be an @AspectJ aspect
   factory.addAspect(usageTracker);	
   // now get the proxy object...
   MyInterfaceType proxy = factory.getProxy();

23.在 Spring 应用中使用 AspectJ。
  <context:spring-configured />
  
  <bean class="org.springframework.beans.factory.aspectj.AnnotationBeanConfigurerAspect" 
      factory-method="aspectOf"/>

  <bean id="myService" class="com.xzy.myapp.service.MyService"
      depends-on="org.springframework.beans.factory.aspectj.AnnotationBeanConfigurerAspect">
  </bean>

24.在 Spring 应用中使用 AspectJ 加载时织入(LTW)。
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xmlns:context="http://www.springframework.org/schema/context"
     xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-2.5.xsd">
   <context:load-time-weaver weaver-class="org.springframework.instrument.classloading.ReflectiveLoadTimeWeaver"/>
</beans>
```
