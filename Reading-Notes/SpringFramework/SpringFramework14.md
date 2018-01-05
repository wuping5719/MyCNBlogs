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

72.Velocity 和 FreeMarker。
   (1) Context 配置:
   <!--  该 bean 使用一个存放模板文件的根路径来配置 Velocity 环境。 -->
   <bean id="velocityConfig" class="org.springframework.web.servlet.view.velocity.VelocityConfigurer">
      <property name="resourceLoaderPath" value="/WEB-INF/velocity/"/>
   </bean>
   <bean id="viewResolver" class="org.springframework.web.servlet.view.velocity.VelocityViewResolver">
      <property name="cache" value="true"/>
      <property name="prefix" value=""/>
      <property name="suffix" value=".vm"/>
   </bean>
   
   <bean id="freemarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
      <property name="templateLoaderPath" value="/WEB-INF/freemarker/"/>
   </bean>

   (2) 高级配置:
   <bean id="freemarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
      <property name="templateLoaderPath" value="/WEB-INF/freemarker/"/>
      <property name="freemarkerVariables">
         <map>
            <entry key="xml_escape" value-ref="fmXmlEscape"/>
         </map>
      </property>
   </bean>
   <bean id="fmXmlEscape" class="freemarker.template.utility.XmlEscape"/>

   (3) 绑定支持和表单处理:
   用于绑定的宏: org.springframework.web.servlet.view.velocity 包中的 spring.vm 文件和 
org.springframework.web.servlet.view.freemarker 包中的 spring.ftl 文件。 

73.XSLT。
   XSLT 是一种用于 XML 的转换语言，并作为一种在 web 应用中使用的 view 层技术广为人知。
   把模型数据转化为 XML:
   public class HomePage extends AbstractXsltView {
      protected Source createXsltSource(Map model, String rootName, HttpServletRequest
        request, HttpServletResponse response) throws Exception {
          Document document = DocumentBuilderFactory.newInstance().newDocumentBuilder().newDocument();
          Element root = document.createElement(rootName);
          List words = (List) model.get("wordList");
          for (Iterator it = words.iterator(); it.hasNext();) {
             String nextWord = (String) it.next();
             Element wordNode = document.createElement("word");
             Text textNode = document.createTextNode(nextWord);
             wordNode.appendChild(textNode);
             root.appendChild(wordNode);
          }
          return new DOMSource(root);
      }
   }
```
