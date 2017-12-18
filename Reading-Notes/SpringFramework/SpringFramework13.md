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
```
