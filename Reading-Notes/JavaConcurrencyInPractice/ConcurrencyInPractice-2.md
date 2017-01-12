<h2>《Java 并发编程实战》 :books: </h2> 
> Brian Goetz、 Tim Peierls、 Joshua Bloch、 Joseph Bowbeer、 David Holmes、 Doug Lea 著   

```html
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

```
