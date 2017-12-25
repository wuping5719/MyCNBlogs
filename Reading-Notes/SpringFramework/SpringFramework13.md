<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
59.DispatcherServlet。
   Spring 的 Web 框架围绕 DispatcherServlet 设计。 DispatcherServlet 的作用是将请求分发到不同的处理器。 
Spring 的 Web 框架包括可配置的处理器(Handler)映射、视图(View)解析、
本地化(Local)解析、主题(Theme)解析以及对文件上传的支持。
Spring 的 Web 框架中缺省的处理器是 Controller 接口，这是一个非常简单的接口，
仅包含 ModelAndView handleRequest(request, response) 方法。
可以通过实现这个接口来创建自己的控制器(也可以称之为处理器)，但是更推荐继承 Spring 提供的一系列控制器，
比如 AbstractController、AbstractCommandController 和 SimpleFormController。
   DispatcherServlet 处理请求的全过程：
   (1) 找到 WebApplicationContext 并将其绑定到请求的一个属性上， 
以便控制器和处理链上的其它处理器能使用 WebApplicationContext。 
默认的属性名为 DispatcherServlet.WEB_APPLICATION_CONTEXT_ATTRIBUTE。 
   (2) 将本地化解析器(LocalResolver)绑定到请求上，
这样使得处理链上的处理器在处理请求(准备数据、显示视图等)时能进行本地化处理。
若不使用本地化解析器，也不会有任何副作用，因此如果不需要本地化解析，忽略它即可。 
   (3) 将主题解析器绑定到请求上，这样视图可以决定使用哪个主题。如果你不需要主题，可以忽略它，不会有任何影响。
   (4) 如果上传文件解析器被指定，Spring 会检查每个接收到的请求是否存在上传文件，
如果存在，这个请求将被封装成 MultipartHttpServletRequest 以便被处理链中的其它处理器使用。
   (5) 找到合适的处理器，执行和这个处理器相关的执行链(预处理器，后置处理器，控制器)，以便为视图准备模型数据(用于渲染)。
   (6) 如果模型数据被返回，就使用配置在 WebApplicationContext 中的视图解析器显示视图，否则视图不会被显示。
有多种原因可以导致返回的数据模型为空，比如预处理器或后处理器可能截取了请求，
这可能是出于安全原因，也可能是请求已经被处理，没有必要再处理一次。

60.Spring Web MVC框架的特点。
   (1) 清晰的角色划分：控制器(Controller)、验证器(Validator)、 命令对象(Command Object)、
表单对象(Form Object)、模型对象(Model Object)、 Servlet 分发器(DispatcherServlet)、 处理器映射(Handler Mapping)、
视图解析器(View Resolver)等等。每一个角色都可以由一个专门的对象来实现。
   (2) 强大而直接的配置方式：将框架类和应用程序类都能作为 JavaBean 配置，支持跨多个 Context 的引用，
例如，在 Web 控制器中对业务对象和验证器(Validator)的引用。
   (3) 可适配、非侵入：可以根据不同的应用场景，选择合适的控制器子类
(simple 型、command型、form型、wizard型、multi-action型或者自定义)，而不是从单一控制器(比如Action/ActionForm)继承。
   (4) 可重用的业务代码：可以使用现有的业务对象作为命令或表单对象，而不需要去扩展某个特定框架的基类。
   (5) 可定制的绑定(binding)和验证(validation)：比如将类型不匹配作为应用级的验证错误，这可以保存错误的值。
再比如本地化的日期和数字绑定等等。在其他某些框架中，你只能使用字符串表单对象，需要手动解析它并转换到业务对象。
   (6) 可定制的 Handler Mapping 和 View Resolution：Spring 提供从最简单的 URL 映射，到复杂的、专用的定制策略。
与某些 Web MVC 框架强制开发人员使用单一特定技术相比，Spring 显得更加灵活。 
   (7) 灵活的 Model 转换：在 SpringWeb 框架中，使用基于 Map 的键/值对来达到轻易地与各种视图技术的集成。 
   (8) 可定制的本地化和主题(Theme)解析：支持在 JSP 中可选择地使用 Spring 标签库、支持 JSTL、支持 Velocity 等等。
   (9) 简单而强大的 JSP 标签库(Spring Tag Library)：支持包括诸如数据绑定和主题(Theme)之类的许多功能。
   (10) JSP 表单标签库：在 Spring2.0 中引入的表单标签库，使得在 JSP 中编写表单更加容易。
   (11) Spring Bean 的生命周期可以被限制在当前的 HTTP Request 或者 HTTP Session。
准确的说，这并非 Spring MVC 框架本身特性，而应归属于 Sping MVC 使用的 WebApplicationContext 容器。 

61.控制器。
   (1) Spring 控制器架构的基础是 org.springframework.mvc.Controller 接口。
   public interface Controller {
      // Process the request and return a ModelAndView object which the DispatcherServlet will render.
      ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception;
   }

   (2) AbstractController 和 WebContentGenerator。
   package samples;
   public class SampleController extends AbstractController {
      public ModelAndView handleRequestInternal(HttpServletRequest request,
            HttpServletResponse response) throws Exception {
         ModelAndView mav = new ModelAndView("hello");
         mav.addObject("message", "Hello World!");
         return mav;
      }
   }
   <bean id="sampleController" class="samples.SampleController">
      <property name="cacheSeconds" value="120"/>
   </bean>

   (3) MultiActionController。
   MultiActionController 有两种使用方式：一是创建 MultiActionController 的子类，
并指定将被 MethodNameResolver 解析的方法(这种情况下不需要这个 delegate 参数)；
二是定义一个委托对象，MethodNameResolver 解析出目标方法后将调用该对象的相应方法。
这种情况下需要定义 MultiActionController 的实例并将委托对象作为协作者注入(可通过构造参数或者 setDelegate 方法)。
   // 'anyMeaningfulName' 指任意方法名
   public [ModelAndView | Map | void] anyMeaningfulName(HttpServletRequest,
                                          HttpServletResponse [,HttpSession] [,AnyObject])
   (4) 命令控制器。
   AbstractCommandController － 可以使用该抽象命令控制器来创建自己的命令控制器，
它能够将请求参数绑定到指定的命令对象。这个类并不提供任何表单功能，
但是它提供验证功能，并且让你在控制器中去实现如何处理由请求参数值产生的命令对象。
   AbstractFormController － 一个支持表单提交的抽象控制器类。
使用这个控制器，可以定义表单，并使用从控制器获取的数据对象构建表单。
当用户输入表单内容，AbstractFormController 将用户输入的内容绑定到命令对象，验证表单内容，并将该对象交给控制器，
完成相应的操作。它支持的功能有防止重复提交、表单验证以及一般的表单处理流程。
子类需要实现自己的方法来指定采用哪个视图来显示输入表单，哪个视图显示表单正确提交后的结果。
如果需要表单，但不想在应用上下文中指定显示给用户的视图，可使用该控制器。
   SimpleFormController － 这是一个 Form Controller，当需要根据命令对象来创建相应的 Form 的时候，
该类可以提供更多的支持。可以为其指定一个命令对象，显示表单的视图名，当表单提交成功后显示给用户的视图名等等。
   AbstractWizardFormController － 这是一个抽象类，
继承这个类需要实现 validatePage()、processFinish() 和 processCancel()方法。
   你有可能也需要写一个 Contractor，它至少需要调用 setPages() 和 setCommandName() 方法。
setPages() 的参数是一个 String 数组，这个数组包含了组成向导的视图名。
setCommandName() 的参数是一个 String，该参数将用来在视图中调用你的命令对象。

62.处理器映射(Handler Mapping)。
   (1) BeanNameUrlHandlerMapping。
    <beans>
      <bean id="handlerMapping" class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
      <bean name="/editaccount.form" class="org.springframework.web.servlet.mvc.SimpleFormController">
         <property name="formView" value="account"/>
         <property name="successView" value="account-created"/>
         <property name="commandName" value="account"/>
         <property name="commandClass" value="samples.Account"/>
      </bean>
    <beans>

  (2) SimpleUrlHandlerMapping。
   <beans>
      <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
         <property name="mappings">
             <value>
               /*/account.form=editAccountFormController
               /*/editaccount.form=editAccountFormController
               /ex/view*.html=helpController
               /**/help.html=helpController
             </value>
         </property>
      </bean>
      <bean id="helpController" class="org.springframework.web.servlet.mvc.UrlFilenameViewController"/>
      <bean id="editAccountFormController" class="org.springframework.web.servlet.mvc.SimpleFormController">
         <property name="formView" value="account"/>
         <property name="successView" value="account-created"/>
         <property name="commandName" value="Account"/>
         <property name="commandClass" value="samples.Account"/>
      </bean>
   <beans>

  (3) 拦截器(HandlerInterceptor)。
    处理器映射中的拦截器必须实现 org.springframework.web.servlet 包中的 HandlerInterceptor 接口。

63.视图与视图解析。
   ViewResolver 和 View 是 Spring 的视图处理方式中特别重要的两个接口。 
ViewResolver 提供了从视图名称到实际视图的映射。View 处理请求的准备工作，并将该请求提交给某种具体的视图技术。 
   (1) 视图解析器(ViewResolver)。
    ① AbstractCachingViewResolver：抽象视图解析器实现了对视图的缓存。在视图被使用之前，通常需要进行一些准备工作。
从它继承的视图解析器将对要解析的视图进行缓存。 
    ② XmlViewResolver：XmlViewResolver 实现 ViewResolver，支持XML格式的配置文件。 
该配置文件必须采用与 Spring XML Bean Factory 相同的 DTD。默认的配置文件是 /WEB-INF/views.xml。 
    ③ ResourceBundleViewResolver：ResourceBundleViewResolver 实现 ViewResolver， 
在一个 ResourceBundle 中寻找所需 bean 的定义。 这个 bundle 通常定义在一个位于 classpath 中的属性文件中。
默认的属性文件是 views.properties。
    ④ UrlBasedViewResolver：UrlBasedViewResolver 实现 ViewResolver，
将视图名直接解析成对应的 URL，不需要显式的映射定义。 
如果你的视图名和视图资源的名字是一致的，就可使用该解析器，而无需进行映射。
    ⑤ InternalResourceViewResolver：作为 UrlBasedViewResolver 的子类， 
它支持 InternalResourceView(对 Servlet 和 JSP 的包装)， 以及其子类 JstlView 和 TilesView。 
通过 setViewClass 方法，可以指定用于该解析器生成视图使用的视图类。 
    ⑥ VelocityViewResolver / FreeMarkerViewResolver: 作为 UrlBasedViewResolver 的子类， 
它能支持 VelocityView(对 Velocity 模版的包装)和 FreeMarkerView 以及它们的子类。

   (2) 视图解析链。
   <bean id="jspViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
       <property name="viewClass" value="org.springframework.web.servlet.view.JstlView"/>
       <property name="prefix" value="/WEB-INF/jsp/"/>
       <property name="suffix" value=".jsp"/>
   </bean>
   <bean id="excelViewResolver" class="org.springframework.web.servlet.view.XmlViewResolver">
       <property name="order" value="1"/>
       <property name="location" value="/WEB-INF/views.xml"/>
   </bean>
   
   (3) 重定向(Rediret)到另一个视图。
    RedirectView 会调用 HttpServletResponse.sendRedirect()方法， 其结果是给用户的浏览器发回一个HTTP redirect。
    ① redirect:前缀。
    ② forward:前缀。

64.本地化解析器。
   本地化解析器和拦截器都定义在 org.springframework.web.servlet.i18n 包中，可以在应用的上下文中配置它们。
   (1) AcceptHeaderLocaleResolver: 检查请求中客户端浏览器发送的 accept-language 头信息， 
通常这个 HTTP Header 包含客户端操作系统的本地化信息。
   (2) CookieLocaleResolver: 检查客户端中的 Cookie 是否包含本地化信息。
当配置这个解析器的时候，可以指定 Cookie 名，以及 Cookie 的最长生存期(Max Age)。 
   (3) SessionLocaleResolver: 允许从用户请求相关的 Session 中获取本地化信息。
   (4) LocaleChangeInterceptor: 可以侦测请求中某个特定的参数，
然后调用上下文中的 LocaleResolver 中的 setLocale() 方法，相应地修改本地化信息。 

65.使用主题。
   (1) 如何定义主题?
   为了在 Web 应用中使用主题，需要设置 org.springframework.ui.context.ThemeSource。 
WebApplicationContext 是从 ThemeSource 扩展而来，但是它本身并没有实现 ThemeSource 定义的方法，
它把这些任务转交给别的专用模块。如果没有明确设置，真正实现 ThemeSource 的类是 
org.springframework.ui.context.support.ResourceBundleThemeSource。 
   <%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
   <html>
   <head>
      <link rel="stylesheet" href="<spring:theme code="styleSheet"/>" type="text/css"/>
   </head>
   <body background="<spring:theme code="background"/>"></body>
   </html>

   (2) 主题解析器。
   ThemeResolver 的实现:
   ① FixedThemeResolver: 选用一个固定的主题，这个主题由 “defaultThemeName” 属性决定。
   ② SessionThemeResolver: 主题保存在用户的 HTTP Session。
在每个 Session 中，这个主题只需要被设置一次，但是每个新 Session 的主题都要重新设置。
   ③ CookieThemeResolver: 用户所选择的主题以 Cookie 的形式存在客户端的机器上面。
   Spring 也支持一个叫 ThemeChangeInterceptor 的请求拦截器。它可以根据请求中包含的参数来动态地改变主题。 

66.Spring 对分段文件上传(multipart file upload)的支持。
   (1) 使用 MultipartResolver。
   <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
      <!-- one of the properties available; the maximum file size in bytes -->
      <property name="maxUploadSize" value="100000"/>
   </bean>
   <bean id="multipartResolver" class="org.springframework.web.multipart.cos.CosMultipartResolver">
      <property name="maxUploadSize" value="100000"/>
   </bean>

   (2) 在表单中处理分段文件上传。
   <form method="post" action="upload.form" enctype="multipart/form-data">
      <input type="file" name="file"/>
      <input type="submit"/>
   </form>
   
   <beans>
	   <!-- lets use the Commons-based implementation of the MultipartResolver interface -->
     <bean id="multipartResolver"
         class="org.springframework.web.multipart.commons.CommonsMultipartResolver"/>
     <bean id="urlMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
        <property name="mappings">
            <value>
                /upload.form=fileUploadController
            </value>
        </property>
     </bean>
     <bean id="fileUploadController" class="examples.FileUploadController">
        <property name="commandClass" value="examples.FileUploadBean"/>
        <property name="formView" value="fileuploadform"/>
        <property name="successView" value="confirmation"/>
     </bean>
   </beans>

67.使用 Spring 的表单标签库。
   (1) 配置。
   Spring 的表单标签库包含在 spring.jar 中。 这个库的描述符(Descriptor)叫做 spring-form.tld。 
   要使用这个库中的标签，在 JSP 页面的开头加入下面声明：
   <%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
 
   (2) form 标签。
   (3) input 标签。
   (4) checkbox 标签。
   (5) checkboxes 标签。
   这个标签生成多个 “checkbox” 类型的 HTML “input” 标签。
   (6) radiobutton 标签。
   (7) radiobuttons 标签。
   这个标签生成多个 “radio” 类型的 HTML “input” 标签。
   (8) password 标签。
   (9) select 标签。
   (10) option 标签。
   (11) options 标签。
   (12) textarea 标签。
   (13) hidden 标签。
   (14) errors 标签。

68.惯例优先原则(Convention Over Configuration)。
   (1) 对控制器的支持：ControllerClassNameHandlerMapping。
   ControllerClassNameHandlerMapping 类是 HandlerMapping 接口的一个实现。
它使用惯例来确定请求的 URL 和用于处理它们的 Controller 实例间的映射关系。
   (2) 对模型的支持：ModelMap(ModelAndView)。
   ModelMap 类首先是一个绚丽的 Map 实现，它可以使新增的将要显示在 View 中(或上)的对象也遵循同一命名规范。
   (3) 对视图的支持：RequestToViewNameTranslator。
   RequestToViewNameTranslator 接口的功能是当没有显式的提供这样一个逻辑视图名称的时候，
确定一个逻辑的 View 名称。这个接口只有一个实现，精明的命名为 DefaultRequestToViewNameTranslator。 

69.基于注解的控制器配置。
   (1) 建立 dispatcher 实现注解支持。
   自定义 HandlerMappings 或 HandlerAdapters， 
需要自定义对应的 DefaultAnnotationHandlerMapping 或 AnnotationMethodHandlerAdapter。
   (2) 使用 @Controller 定义一个控制器。
   注解 @Controller 指明一个特定的类承担控制器的职责，而没有扩展任何控制器基类或者引用S ervlet API 的必要。
   (3) 使用 @RequestMapping 映射请求。
   注解 @RequestMapping 被用于映射如 “/editPet.do” 这样的 URL 到一个完整的类或者一个特定的处理方法。 
   (4) 使用 @RequestParam 绑定请求参数到方法参数。
   @RequestParam 注解是用于在控制器中绑定请求参数到方法参数。
   (5) 使用 @ModelAttribute 提供一个从模型到数据的链接。
   当作为一个方法参数时，@ModelAttribute 用于映射一个模型属性到特定的注解的方法参数。
这是控制器获得持有表单数据对象引用的方法。@ModelAttribute 也用于在方法级别为模型提供引用数据。
   (6) 使用 @SessionAttributes 指定存储在会话中的属性。
    类型级别的 @SessionAttributes 注解使用一个特定的句柄声明会话属性。 
   (7) 自定义 WebDataBinder 初始化。
   使用 @InitBinder 自定义数据绑定；配置一个定制的 WebBindingInitializer。
```
