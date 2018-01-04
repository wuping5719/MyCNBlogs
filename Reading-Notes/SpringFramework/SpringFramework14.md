<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
70.JSP 和 JSTL。
   视图解析器: 使用 JSP 方式需要一个用来解析视图的视图解析器，常用的是在 WebApplicationContext 中定义的 
InternalResourceViewResolver 和 ResourceBundleViewResolver。 
  <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
     <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
     <property name="prefix" value="/WEB-INF/jsp/"/>
     <property name="suffix" value=".jsp"/>
  </bean>

71.Tiles。
  (1) UrlBasedViewResolver 类: 为它解析的每个 view 实例化一个 viewClass 类的实例。
  <bean id="viewResolver" class="org.springframework.web.servlet.view.UrlBasedViewResolver">
     <property name="viewClass" value="org.springframework.web.servlet.view.tiles2.TilesView"/>
  </bean>

  (2) ResourceBundleViewResolver 类: 需要一个属性文件，其中包含了它需要使用的视图名和视图类。
  <bean id="viewResolver" class="org.springframework.web.servlet.view.ResourceBundleViewResolver">
     <property name="basename" value="views"/>
  </bean>

  (3) SimpleSpringPreparerFactory 和 SpringBeanPreparerFactory:
  基于指定的 preparer 类，指定 SimpleSpringPreparerFactory 自动装配 ViewPreparer 实体，
不但应用 Spring 的容器回调而且应用配置 Spring BeanPostProcessors。
  指定 SpringBeanPreparerFactory 来操作指定 preparer 名称，而不是类，
从 DispatcherServlet 的应用上下文获取相应的 Spring bean。  
```
