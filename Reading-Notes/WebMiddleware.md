<h2>《大型网站系统与Java中间件实践》 :books: </h2> 
> 曾宪杰 著       电子工业出版社

```html
 1.网络 IO 实现方式：         
  (1) BIO 方式：Blocking IO，阻塞 IO，一个 Socket 套接字需要使用一个线程来进行处理。        
  (2) NIO 方式：Nonblocking IO，非阻塞 IO，基于事件驱动思想，采用 Reactor 模式。  
  (3) AIO 方式：Asynchronous IO，异步 IO，采用 Proactor模式。

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
  
 4.Java 并发编程的类、接口和方法：
  (1) 线程池：ThreadPoolExecutor 和定时线程池 ScheduledThreadPoolExecutor。
  (2) synchronized。
  (3) ReentrantLock 可重入锁和 ReentrantReadWriteLock。
      lock.lock();
      try {
         // do something
      }
      finally {
         lock.unlock();
      }
  (4) volatile：实现变量可见性，但不保证原子性。  
  (5) Atomics：原子类。  
  (6) wait、notify、notifyAll。 
      public void testWait() throws InterruptedException {
         synchronized (this) {
            this.wait();
         }
      }
      public void testNotify() {
         synchronized (this) {
            this.notify();
         }
      }
  (7) CountDownLatch：当多个线程都达到了预期状态或完成预期工作时触发事件。
  (8) CyclicBarrier：协同多个线程，让多个线程在这个屏障前等待，直到所有线程都到达了这个屏障时，再一起继续执行后面动作。
```
