<h2>《Java 并发编程实战》 :books: </h2> 

> Brian Goetz、 Tim Peierls、 Joshua Bloch、 Joseph Bowbeer、 David Holmes、 Doug Lea 著   

```java
 二.结构化并发应用程序
  22.任务执行-在线程中执行任务：
    串行地执行任务。
    显示地为任务创建线程。
    无限制创建线程的不足：线程生命周期的开销非常高；资源消耗；稳定性。
    
  23.Executor 框架：
    (1) Executor 接口：
    public interface Executor {
       void execute(Runnable command);
    }
    
    (2) 基于 Executor 的 Web 服务器：
    class TaskExecutionWebServer {
       private static final int NTHREADS = 100;
       private static final Executor exec = Executors.newFixedThreadPool(NTHREADS);
       
       public static void main(String[] args) throws IOException {
          ServerSocket socket = new ServerSocket(80);
          while(true) {
             final Socket connection = socket.accept();
             Runnable task = new Runnable() {
                public void run() {
                   handleRequest(connection);
                }
             };
             exec.execute(task);
          }
       }
    }
    
    为每一个请求启动一个新线程的 Executor。
    public class ThreadPerTaskExecutor implements Executor {
       public void execute(Runnable r) {
          new Thread(r).start();
       };
    }
    
    在调用线程中以同步方式执行所有任务的 Executor。
    public class WithinThreadExecutor implements Executor {
       public void execute(Runnable r) {
          r.run();
       };
    }
    
    (3) 执行策略：
      每当看到下面这种形式的代码时： new Thread(runnable).start(); 
    并且你希望获得一种更灵活的执行策略时，请考虑使用 Executor 来代替 Thread。
    
    (4) 线程池：
      可以通过调用 Executors 中的静态工厂方法来创建一个线程池。
      newFixedThreadPool：创建一个固定长度的线程池。
      newCachedThreadPool：创建一个可缓存的线程池。
      newSingleThreadExecutor：一个单线程的 Executor，它创建单个工作线程来执行任务，如果这个线程异常结束，
    会创建另一个线程来替代。
      newScheduledThreadPool：创建一个固定长度的线程池，而且以延迟或定时的方式来执行任务。
    
    (5) Executor的生命周期：
    ExecutorService 中的生命周期管理方法：
    public interface ExecutorService extends Executor {
      void shutdown();
      List<Runnable> shutdownNow();
      boolean isShutdown();
      boolean isTerminated();
      boolean awaitTermination(long timeout, TimeUnit unit) throws InterrupteException;
    }

    支持关闭操作的 Web 服务器：
    class LifecycleWebServer {
      private final ExecutorService exec = ...;
     
      public void start() throws IOException {
           ServerSocket socket = new ServerSocket(80);
           while(!exec.isShutdown()) {
              try {
                 final Socket conn = socket.accept();
                 exec.execute(new Runnable() {
                    public void run() { handleRequest(conn); }
                 });
              } catch (RejectedExecutorException e) {
                 if (!exec.isShutdown())
                   log("task submission rejected", e);
            }
         }
      }

      public void stop() { exec.shutdown(); }

      void handleRequest(Socket connection) {
         Request req = readRequest(connection);
         if(isShutdownRequest(req))
            stop();
         else
            dispatchRequest(req);
      }
    }
    
   (6) 延迟任务与周期任务。
   
  24.找出可利用的并行性：
   (1) 携带结果的任务 Callable 与 Future。
   Callable 与 Future接口：
   public interface Callable<V> {
     V call() throws Exception;
   }

   public interface Future<V> {
     boolean cancel(boolean mayInterruptedIfRunning);
     boolean isCanncelled();
     boolean isDone();
     V get() throws InterruptedException, ExecutionException, CancellationException;
     V get(long timeout, TimeUnit unit) throws InterruptedException, 
                        ExecutionException, CancellationException, TimeoutException;
   }
    
   (2) 在异构任务并行化中存在的局限。
   (3) CompletionService：Executor 与 BlockingQueue。
   使用 CompletionService，使页面元素在下载完成后立即显示出来。
   public class Renderer {
     private final ExecutorService executor;

     Renderer(ExecutorService executor) { this.executor = executor; }

     void renderPage(CharSequence source) {
       List<ImageInfo> info = scanForImageInfo(source);
       CompletionService<ImageData> completionService = new ExecutorCompletionService<ImageData>(executor);
          for(final ImageInfo imageInfo : info)
              completionService.submit(new Callable<ImageData>() {
                  public ImageData call() {
                     return imageInfo.downloadImage();
                  }
              });

       renderText(source);

       try {
          for(int t = 0; n = info.size(); t < n; t++) {
             Future<ImageData> f = completionService.take();
             ImageData imageData = f.get();
             renderImage(imageData);
          }
       } catch (InterruptedException e) {
          Thread.currentThread().interrupt();
       } catch (ExecutionException e) {
          throw launderThrowable(e.getCause());
       }
     }
   }
  
   (4) 为任务设置时限。
   在指定时间内获取广告信息：
   Page renderPageWithAd() throws InterruptedException {
      long endNanos = System.nanoTime() + TIME_BUDGET;
      Future<Ad> f = exec.submit(new FetchAdTask());
      // 在等待广告的同时显示页面
      Page page = renderPageBody();
      Ad ad;
      try {
         // 只等待指定的时间长度
         long timeLeft = endNanos - System.nanoTime();
         ad = f.get(timeLeft, NANOSECONDS);
      } catch (ExecutionException e) {
         ad = DEFAULT_AD;
      } catch (TimeoutException e) {
         ad = DEFAULT_AD;
         f.cancel(true);
      }
      page.setAd(ad);
      return page;
   }
 
   25.任务取消：用户请求取消；有时间限制的操作；应用程序事件；错误；关闭。
   (1) 中断：在 Java 的 API 或语言规范中，并没有将中断与任何取消语义关联起来，但实际上，
    如果在取消之外的其他操作中使用中断，那么都是不合适的，并且很难支撑起来更大的应用。
    Thread 中的中断方法：
    public class Thread {
       public void interrupt() { ... }
       public boolean isInterrupted() { ... }
       public static boolean interrupted() { ... }
    }

    调用 interrupt 并不意味着立即停止目标线程正在进行的工作，而只是传递了请求中断的消息。
    通常，中断是实现取消的最合理方式。
    通过中断来取消：
    class PrimeProducer extends Thread {
       private final BlockingQueue<BigInteger> queue;

       PrimeProducer(BlockingQueue<BigInteger> queue) {
         this.queue = queue;
       }

       public void run() {
          try {
             BigInteger p = BigInteger.ONE;
             while(!Thread.currentThread().isInterrupted())
               queue.put(p = p.nextProbablePrime());
          } catch (InterruptedException consumed) {
            // 允许线程退出
          }
       }
       public void cancel() { interrupt(); }
    }

   (2) 中断策略：
     由于每个线程都拥有各自的中断策略，因此除非你知道中断对该线程的含义，否则就不应该中断这个线程。
   (3) 响应中断：
     只有实现了线程中断策略的代码才可以屏蔽中断请求。在常规的任务和库代码中都不应该屏蔽中断请求。
     不可取消的任务在退出前恢复中断：
     public Task getNextTask(BlockingQueue<Task> queue) {
        boolean interrupted = false;
        try {
           while (true) {
              try {
                return queue.take();
              } catch (InterruptedException e) {
                 interrupted = true;
                 // 重新尝试
              }
           }
         } finally {
            if (interrupted)
              Thread.currentThread().interrupted();
        }
     }
   
   (4) 通过 Future 来实现取消：
   public static void timedRun(Runnable r, long timeout, TimeUnit unit) throws InterruptedException {
      Future<?> task = taskExec.submit(r);
      try {
         task.get(timeout, unit);
      } catch (TimeoutException e) {
         // 接下来的任务将被取消
      } catch (ExecutionException e) {
         // 如果在任务中抛出了异常，那么重新抛出该异常
         throw launderThrowable(e.getCause());
      } finally {
         // 如果任务已经结束，那么执行取消操作也不会带来任何影响
         task.cancel(true);  // 如果任务正在运行，那么将被中断
      }
   }
    当 Future.get 抛出 InterruptedException 或 TimeoutException 时，如果你知道不再需要结果，
  那么就可以调用 Future.cancel 来取消任务。

   (5) 处理不可中断的阻塞：Java.io 包中的同步 Socket I/O；Java.io 包中的同步 I/O；Selector 的异步 I/O；获取某个锁。
   (6) 采用 newTaskFor 来封装非标准的取消。
   
 26.停止基于线程的服务。
   对于持有线程的服务，只要服务的存在时间大于创建线程的方法的存在时间，那么就应该提供生命周期方法。
  (1) 关闭 ExecutorService。 
  使用 ExecutorService 的日志服务：
  public class LogService {
     private final ExecutorService exec = new SingleThreadExecutor();
     ...
     public void start() { }

     public void stop() throws InterruptedException {
        try {
           exec.shutdown();
           exec.awaitTermination(TIMEOUT, UNIT);
        } finally {
           writer.close();
        }
     }
     public void log(String msg) {
        try {
           exec.execute(new WriteTask(msg));
        } catch (RejectedExecutionException ignored) { }
     }
  }

  (2) “毒丸”对象。 
  通过“毒丸”对象来关闭服务。
  public class IndexingService {
     private static final File POISON = new File("");
     private final IndexerThread consumer = new IndexerThread();
     private final CrawlerThread producer = new CrawlerThread();
     private final BlockingQueue<File> queue;
     private final FileFilter fileFilter;
     private final File root;

     // IndexingService 消费者线程
     class IndexerThread extends Thread {
        public void run() {
           try {
              while(true) {
                 File file = queue.take();
                 if(file == POISON)
                    break;
                 else 
                    indexFile(file);
              }
           } catch (InterruptedException consumed) { }
        }
     }

     // IndexingService 生产者线程
     class CrawlerThread extends Thread {
        public void run() {
           try {
              crawl(root);
           } catch (InterruptedException e) { // 发生异常 }
           finally {
              while(true) {
                 try {
                    queue.put(POISON);
                    break;
                 } catch (InterruptedException e1) { // 重新尝试 }
              }
           }
        }

        private void crawl(File root) throws InterruptedException { ... }
     }

     public void start() {
        producer.start();
        consumer.start();
     }

     public void stop() { producer.interrupt(); }

     public void awaitTermination() throws InterruptedException {
        consumer.join();
     }
  }

  (3) shutdownNow 的局限性。 

 27.处理非正常的线程终止。
  典型的线程池工作者线程结构：
  public void run() {
    Throwable thrown = null;
    try {
       while (!isInterrupted())
         runTask(getTaskFromWorkQueue());
    } catch (Throwable e) {
       thrown = e;
    } finally {
       threadExited(this, thrown);
    }
  }
  未捕获异常的处理：在运行时间较长的应用程序中，通常会为所有线程的未捕获异常指定同一个异常处理器，
并且该处理器至少会将异常信息记录到日志中。

28.JVM 关闭。
  (1) 关闭钩子。
  通过注册一个关闭钩子来停止日志服务。
  public void start() {
     Runtime.getRuntime().addShutdownHook(new Thread() {
       public void run() {
          try { LogService.this.stop(); }
          catch (InterruptedException ignored) {}
       }
     });
  }
  (2) 守护线程：守护线程通常不能用于替代应用程序管理程序中各个服务的生命周期。
  (3) 终结器：避免使用终结器。
  
 29.线程池的使用：在任务与执行策略之间的隐性耦合。
   需要明确地指定执行策略的任务类型有：依赖性任务；使用线程封闭机制的任务；对响应时间敏感的任务；使用 ThreadLocal的任务.
   在一些任务中，需要拥有或排除某种特定的执行策略。如果某些任务依赖于其他的任务，那么会要求线程池足够大，
 从而确保它们依赖任务不会被放入等待队列中或被拒绝，而采用线程封闭机制的任务需要串行执行。通过将这些需求写入文档，
 将来的代码维护人员就不会由于使用了某种不合适的执行策略而破坏安全性或活跃性。
   (1) 线程饥饿死锁。
   每当提交一个有依赖性的 Executor 任务时，要清楚地知道可能会出现线程“饥饿”死锁，因此需要在代码或
 配置 Executor 的配置文件中记录线程池的大小限制或配置限制。
   (2) 运行时间较长的任务。

 30.设置线程池的大小：
    Ncpu = number of CPUs;
    Ucpu = target CPU utilization, 0 ≦ Ucpu ≦ 1;
    W / C = ratio of wait time to compute time.
   要使处理器达到期望的使用率，线程池的最优大小等于：
    Nthreads = Ncpu * Ucpu * (1 +  W / C).

 31.配置 ThreadPoolExecutor：
   ThreadPoolExecutor 的通用构造函数：
   public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, 
             TimeUnit unit, BlockingQueue<Runnable> workQueue, 
             ThreadFactory threadFactory, RejectExecutionHandler handler) { ... }
   (1) 线程的创建与销毁。
   (2) 管理队列任务。
   对于 Executor，newCachedThreadPool 工厂方法是一种很好的默认选择，它能提供比固定大小的线程池更好的排队性能。
 当需要限制当前任务的数量以满足资源管理需求时，那么可以选择固定大小的线程池，就像在接受网络客户请求的服务器应用程序中，
 如果不进行限制，那么容易发生过载问题。
   (3) 饱和策略。
   使用 Semaphore 来控制任务的提交速率。
   public class BoundedExecutor {
      private final Executor exec;
      private final Semaphore semaphore;

      public BoundedExecutor(Executor exec, int bound) {
         this.exec = exec;
         this.semaphore = new Semaphore(bound);
      } 

      public void submitTask(final Runnable command) throws InterruptedException {
         semaphore.acquire();
         try {
            exec.execute(new Runnable() {
               public void run() {
                  try {
                     command.run();
                  } finally {
                     semaphore.release();
                  }
               }
            });
         } catch (RejectExecutionException e) {
            semaphore.release();
         }
      }
   }

   (4) 线程工厂。
   ThreadFactory 接口：
   public interface ThreadFactory {
      Thread newThread(Runnable r);
   }
   (5) 在调用构造函数后再定制 ThreadPoolExecutor。

 32.扩展ThreadPoolExecutor：
   增加了日志和计时等功能的线程池：
   public class TimingThreadPool extends ThreadPoolExecutor {
      private final ThreadLocal<Long> startTime = new ThreadLocal<Long>();
      private final Logger log = Logger.getLogger("TimingThreadPool");
      private final AtomicLong numTasks = new AtomicLong();
      private final AtomicLong totalTime = new AtomicLong();

      protected void beforeExecute(Thread t, Runnable r) {
         super.beforeExecute(t, r);
         log.fine(String.format("Thread %s: Start %s", t, r));
         startTime.set(System.nanoTime());
      }

      protected void afterExecute(Runnable r, Throwable t) {
         try {
            long endTime = System.nanoTime();
            long taskTime = endTime - startTime.get();
            numTasks.incrementAndGet();
            totalTime.addAndGet(taskTime);
            log.fine(String.format("Thread %s: end %s, time=%dns", t, r, taskTime));
         } finally {
            super.afterExecute(r, t);
         }
      }

      protected void terminated() {
         try {
            log.info(String.format("Terminated: avg time=%dns", totalTime.get() / numTasks.get()));
         } finally {
            super.terminated();
         }
      }
   }
   
  33.递归算法的并行化。
  
  34.图形用户界面应用程序：
    所有的 GUI 框架基本上都实现为单线程的子系统，其中所有与表现相关的代码都作为任务在事件线程中运行。
  由于只有一个事件线程，因此运行时间较长的任务会降低 GUI 程序的响应性，所以应该在后台线程中运行。
  在一些辅助类(例如：SwingWorker)中提供了对取消、进度指示以及完成指示的支持，因此对于执行时间较长的任务来说，
  无论在任务中包含了 GUI 组件还是非 GUI 组件，在开发时都可以得到简化。
    Swing 的单线程规则是：Swing 中的组件以及模型只能在这个事件分发线程中进行创建、修改以及查询。
    共享数据模型-如果一个数据模型必须被多个线程共享，而且由于阻塞、一致性或复杂度等原因而无法实现一个线程安全的模型时，
  可以考虑使用分解模型设计。
```
