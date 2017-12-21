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
```
