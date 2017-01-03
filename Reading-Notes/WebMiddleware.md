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
  (9) Semaphore：信号量。
      semaphore.acquire();
      try {
         //调用远程通信的方法
      }
      finally {
         semaphore.release();
      }
  (10) Exchanger：用于在两个线程之间进行数据交换。
  (11) Future 和 FutureTask。
  (12) 并发容器：CopyOnWrite。
  
 5.动态代理：
   public void testDynamicProxy() {
      Calculator calculator = new CalculatorImpl();
      LogHandler lh = new LogHandler(calculator);
      Calculator proxy = (Calculator) Proxy.newProxyInstance(calculator.getClass().getClassLoader(),
                   calculator.getClass().getInterfaces(), lh);
      proxy.add(1, 1);
   }
   
   public class LogHandler implements InvocationHandler {
      Object obj;
      LogHandler(Object obj) {
         this.obj = obj;
      }
      public Object invoke(Object obj1, Method method, Object[] args) throws Throwable {
         this.doBefore();
         Object o = method.invoke(obj, args);
         this.doAfter();
         return o;
      }
      public void doBefore() {
         System.out.println("do this before");
      }
      public void doAfter() {
         System.out.println("do this after");
      }
   }
   
 6.反射：
   (1) 获取对象属于哪个类.
     Class class = object.getClass();
   (2) 获取类的信息.
     String className = class.getName();                 //获取类名
     Method[] methods = class.getDeclaredMethods();      //获取类中定义的方法
     Field[] fields = class.getDeclaredFields();         //获取类中定义的成员
   (3) 构建对象.
     Class.forName("ClassName").newInstance();
   (4) 动态执行方法.
     Method method = class.getDeclaredMethod("add", int.class, int.class);
     method.invoke(this, 1, 1);
   (5) 动态操作属性.
     Field field = class.getDeclaredField("name");
     field.set(this, "Test");
     
  7.数据库从单机到分布式的挑战与应对：
    1) 从应用使用单机数据库开始。
    2) 数据库垂直/水平拆分的困难。
    3) 单机变为多机后，事务如何处理。
     (1) 大型网站一致性的基础理论——CAP/BASE。
      CAP：Consistency：一致性、Availability：可用性、Partition-Tolerance：分区容忍性。
     (2) 比两阶段提交更轻量一些的 Paxos 协议。
     (3) 集群内数据一致性的算法：Quorum 和 Vector Clock算法。
    4) 多机的 Sequence 问题与处理。
      方案：把所有的 ID 集中存放在一个地方进行管理，对每个 ID 序列独立管理，每台机器使用 ID 时都从这个
        ID 生成器上取。
    5) 应对多机的数据查询。
     (1) 跨库 Join。
     (2) 外键约束。
     (3) 分库分表后查询：非排序分页——A.等步长分页，B.等比例分页。
     
   8.数据层结构图：
   <http://images.cnblogs.com/cnblogs_com/wp5719/831982/o_Data.png>
```
