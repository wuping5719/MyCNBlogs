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
```
