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
```
