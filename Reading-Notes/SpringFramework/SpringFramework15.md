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
```
