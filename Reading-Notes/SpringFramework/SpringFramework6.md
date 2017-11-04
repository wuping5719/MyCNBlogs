<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
25.Spring 中的切入点 API。
  (1) org.springframework.aop.Pointcut 是最核心的接口，用来将通知应用于特定的类和方法。
  public interface Pointcut {
     ClassFilter getClassFilter();
     MethodMatcher getMethodMatcher();
  }
  ClassFilter 接口用来将切入点限定在一个给定的类集合中。
  public interface ClassFilter {
     boolean matches(Class clazz);  // 如果 matches() 方法总是返回 true，所有目标类都将被匹配.
  }
  
  public interface MethodMatcher {
     // matches(Method, Class) 方法用来测试这个切入点是否匹配目标类的指定方法。 
     boolean matches(Method m, Class targetClass);
     boolean isRuntime();
     boolean matches(Method m, Class targetClass, Object[] args);
  }

26.便利的切入点实现。
  (1) 静态切入点：正则表达式切入点。
  org.springframework.aop.support.Perl5RegexpMethodPointcut 是一个最基本的正则表达式切入点。
  <bean id="settersAndAbsquatulatePointcut" class="org.springframework.aop.support.Perl5RegexpMethodPointcut">
    <property name="patterns">
      <list>
         <value>.*set.*</value>
         <value>.*absquatulate</value>
      </list>
    </property>
  </bean>
  <bean id="settersAndAbsquatulateAdvisor" class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
    <property name="advice">
      <ref local="beanNameOfAopAllianceInterceptor"/>
    </property>
    <property name="patterns">
      <list>
        <value>.*set.*</value>
        <value>.*absquatulate</value>
      </list>
    </property>
  </bean>
  
  (2) 动态切入点：控制流切入点。
  控制流切入点是由 org.springframework.aop.support.ControlFlowPointcut 类声明的。
  
27.Spring 的通知 API。
  (1) 拦截环绕通知: 实现环绕通知的 MethodInterceptor 应当实现下面的接口：
  public interface MethodInterceptor extends Interceptor {
     // invoke() 方法的 MethodInvocation 参数暴露了被调用的方法、目标连接点、AOP 代理以及传递给方法的参数。
     Object invoke(MethodInvocation invocation) throws Throwable;
  }
  
  public class DebugInterceptor implements MethodInterceptor {
     public Object invoke(MethodInvocation invocation) throws Throwable {
        System.out.println("Before: invocation=[" + invocation + "]");
        Object rval = invocation.proceed();
        System.out.println("Invocation returned");
        return rval;
     }
  }
```
