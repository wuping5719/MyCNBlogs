<h2>《大型网站系统与Java中间件实践》 :books: </h2> 
> 曾宪杰 著       电子工业出版社

```html
 1.网络IO实现方式：         
  (1) BIO方式：Blocking IO，阻塞 IO，一个 Socket 套接字需要使用一个线程来进行处理。        
  (2) NIO方式：Nonblocking IO，非阻塞 IO，基于事件驱动思想，采用 Reactor 模式。  
  (3) AIO方式：Asynchronous IO，异步 IO，采用 Proactor模式。

 2.分布式系统的难点：
  (1) 缺乏全局时钟。  
  (2) 面对故障独立性。  
  (3) 处理单点故障。  
  (4) 事务的挑战。  
 
 3.集群 Session 同步：
  (1) Session Sticky：让同样的 Session 请求每次都发送到同一个服务器处理。 
  (2) Session Replication：在 Web 服务器之间增加会话数据同步。
  (3) Session 数据集中存储：把 Session 数据集中存储起来。
  (4) Cookie Based：通过 Cookie 来传递 Session 数据。
```
