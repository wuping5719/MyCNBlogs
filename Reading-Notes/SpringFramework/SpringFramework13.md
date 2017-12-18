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
```
