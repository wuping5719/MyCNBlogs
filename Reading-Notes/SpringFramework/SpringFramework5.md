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
```
