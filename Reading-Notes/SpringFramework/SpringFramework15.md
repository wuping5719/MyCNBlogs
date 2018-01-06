<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
74.JavaServer Faces。
   JavaServer Faces (JSF) 是一个基于组件的，事件驱动的 Web 框架。
   Spring 与 JSF 集成的关键类是 DelegatingVariableResolver。
   (1) DelegatingVariableResolver:
   <faces-config>
     <application>
        <variable-resolver>org.springframework.web.jsf.DelegatingVariableResolver</variable-resolver>
        <locale-config>
           <default-locale>en</default-locale>
           <supported-locale>en</supported-locale>
           <supported-locale>es</supported-locale>
        </locale-config>
        <message-bundle>messages</message-bundle>
     </application>
   </faces-config>

   (2) FacesContextUtils:
   ApplicationContext ctx = FacesContextUtils.getWebApplicationContext(FacesContext.getCurrentInstance());

75.Struts。
   要集成 Struts 与 Spring，有两个选择：
     配置 Spring 将 Action 作为 bean 托管，使用 ContextLoaderPlugin， 并且在 Spring context 中设置依赖关系。
     继承 Spring 的 ActionSupport 类并且使用 getWebApplicationContext() 方法获取 Spring 管理的 bean。
   ContextLoaderPlugin: 为了在 struts-config.xml 文件中配置 DelegatingRequestProcessor，
   需要重载 <controller> 元素的 “processorClass” 属性。
   <action path="/user" type="org.springframework.web.struts.DelegatingActionProxy"
          name="userForm" scope="request" validate="false" parameter="method">
      <forward name="list" path="/userList.jsp"/>
      <forward name="edit" path="/userForm.jsp"/>
   </action>

76.Tapestry。
   (1) Tapestry 是用来创建动态、健壮、高伸缩性 Web 应用的一个 Java 开源框架。
Tapestry 组件构建于标准的 Java Servlet API 之上，所以它可以工作在任何 Servlet 容器或者应用服务器之上。
   (2) WebWork 是一个 Java Web 应用开发框架。这个框架充分考虑了如何提高开发者的效率和简化代码。
它支持构建可重复使用的 UI 模版(例如表单控制)，UI 主题，国际化，动态表单参数映射到 JavaBean，
健壮的客户端与服务器端校验等更多功能。
```
