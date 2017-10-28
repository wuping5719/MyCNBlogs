<h2>《Spring 2.5开发手册》 :books: </h2> 

```java
9.Resource 接口。
  public interface InputStreamSource {
     boolean exists();  // 返回标识这个资源在物理上是否的确存在的 boolean 值
     boolean isOpen();  // 返回标识这个资源是否有已打开流的处理类的 boolean 值
     URL getURL() throws IOException;
     File getFile() throws IOException;
     Resource createRelative(String relativePath) throws IOException;
     String getFilename();
     String getDescription();  // 返回资源的描述，一般在与此资源相关的错误输出时使用
     InputStream getInputStream() throws IOException; // 定位并打开资源，返回读取此资源的一个 InputStream
  }

10.内置 Resource 实现。
   (1) UrlResource：封装了 java.net.URL。
   (2) ClassPathResource：标识从 classpath 获得的资源。
   (3) FileSystemResource：为处理 java.io.File 而准备的 Resource 实现。
   (4) ServletContextResource：为 ServletContext 资源提供的 Resource 实现。
   (5) InputStreamResource：为给定的 InputStream 而准备的 Resource 实现。
   (6) ByteArrayResource：为给定的 byte 数组准备的 Resource 实现。
   
11.ResourceLoader 接口。
  public interface ResourceLoader {
     Resource getResource(String location);
  }
  Resource template = ctx.getResource("classpath:some/resource/path/myTemplate.txt");
  Resource template = ctx.getResource("file:/some/resource/path/myTemplate.txt");
  Resource template = ctx.getResource("http://myhost.com/resource/path/myTemplate.txt");

12.ResourceLoaderAware 接口。
  public interface ResourceLoaderAware {
     void setResourceLoader(ResourceLoader resourceLoader);
  }
  只要属性、构造方法或者方法被 @Autowired 注解修饰，
ResourceLoader 就会被装配到需要 ResourceLoader 类型的属性、构造方法参数或者方法参数中。

13.ApplicationContext 和 Resource 路径。
  (1) 创建 ClassPathXmlApplicationContext：
   ApplicationContext ctx = new ClassPathXmlApplicationContext(
       new String[] {"services.xml", "daos.xml"}, MessengerService.class);
  (2) classpath*: 前缀.
   ApplicationContext ctx = new ClassPathXmlApplicationContext("classpath*:conf/appContext.xml");
  (3) FileSystemResource：
  // force this FileSystemXmlApplicationContext to load it's definition via a UrlResource
  ApplicationContext ctx = new FileSystemXmlApplicationContext("file:/conf/context.xml");
```
