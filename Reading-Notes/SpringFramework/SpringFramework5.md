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
```
