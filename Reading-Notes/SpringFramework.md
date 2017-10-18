<h2>《Spring 2.5开发手册》 :books: </h2> 

> <a href="https://springcloud.cc/spring-cloud-dalston.html">Spring Cloud 中文文档</a>

```java
1.IoC (控制反转) 容器。
  org.springframework.beans.factory.BeanFactory 是 Spring IoC 容器的实际代表者，
IoC 容器负责容纳 bean，并对 bean 进行管理。
  (1) 实例化 bean：
   构造器实例化：<bean id="exampleBean" class="examples.ExampleBean"/>
   静态工厂方法实例化：<bean id="exampleBean" class="examples.ExampleBean" factory-method="createInstance"/>
   实例工厂方法实例化：
    <!-- the factory bean, which contains a method called createInstance() -->
    <bean id="serviceLocator" class="com.foo.DefaultServiceLocator">
        <!-- inject any dependencies required by this locator bean -->
    </bean>
    <!-- the bean to be created via the factory bean -->
    <bean id="exampleBean" factory-bean="serviceLocator" factory-method="createInstance"/>
  (2) 使用容器：
    Resource res = new FileSystemResource("beans.xml");
    BeanFactory factory = new XmlBeanFactory(res);
  (3) 依赖注入（DI）:
    DI 主要有两种注入方式，即 Setter 注入和构造器注入。
```
