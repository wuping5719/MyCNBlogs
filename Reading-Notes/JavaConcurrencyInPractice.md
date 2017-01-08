<h2>《Java 并发编程实战》 :books: </h2> 
> Brian Goetz、 Tim Peierls、 Joshua Bloch、 Joseph Bowbeer、 David Holmes、 Doug Lea 著   

```html
  1.什么是线程安全？
    当多个线程访问某个类时，不管运行时环境采用何种调度方式或者这些线程将如何交替执行，并且在主调代码中不需要任何额外的
  同步和协同，这个类都能表现出正确的行为，那么就称这个类是线程安全的。
    在线程安全类中封装了必要的同步机制，因此客户端无须进一步采取同步措施。
    无状态对象一定是线程安全的。
  
  2.原子性：
    ++count：“读取-修改-写入”的操作序列，其结果状态依赖之前的状态。
    竞态条件：最常见的竞态条件-“先检查后执行(Check-Then-Act)”操作，即通过一个可能失效的观测结果来决定下一步的动作。
    复合操作：“先检查后执行(Check-Then-Act)”和“读取-修改-写入”(例如递增运算)等操作统称为复合操作。
    假设有两个操作A和B，如果从执行A的线程来看，当另一个线程执行B时，要么将B全部执行完，要么完全不执行B，那么A和B对彼此
  来说是原子的。原子操作是指，对于访问同一个状态的所有操作(包括该操作本身)来说，这个操作是一个以原子方式执行的操作。
    在实际情况中，应尽可能地使用现有的线程安全对象(例如AcomicLong)来管理类的状态。与非线程安全的对象相比，判断线程安全
  对象的可能状态及其状态转换情况要更为容易，从而也更容易维护和验证线程安全性。
   
  3.加锁机制：
    要保持状态的一致性，就需要在单个原子操作中更新所有相关的状态变量。
    内置锁：静态的 synchronized 方法以 Class 对象作为锁。
    “重入”意味着获取锁的操作的粒度是“线程”，而不是调用。
  
  4.用锁来保护状态： 
    对于可能被多个线程同时访问的可变状态变量，在访问它时都需要持有同一个锁，在这种情况下，我们称状态变量是由这个锁保护的.
    每个共享的和可变的变量都应该只由一个锁来保护，从而使维护人员知道是哪一个锁。
    对于每个包含多个变量的不变性条件，其中涉及的所有变量都需要由同一个锁来保护。
  
  5.活跃性与性能：
    通常，在简单性与性能之间存在着相互制约因素。当实现某个同步策略时，一定不要盲目地为了性能而牺牲简单性(可能破坏安全性).
    当执行时间较长的计算或者可能无法快速完成的操作时(例如，网络 I/O 或控制台 I/O)，一定不要持有锁。
    
  6.可见性：
    在没有同步的情况下，编译器、处理器以及运行时等都可能对操作的执行顺序进行一些意想不到的调整。在缺乏足够同步的多线程
  程序中，要想对内存操作的执行顺序进行判断，几乎无法得出正确的结论。
    加锁与可见性：加锁的含义不仅仅局限于互斥行为，还包括内存可见性。为了确保所有线程都能看到共享变量的最新值，所有执行
  读操作或者写操作的线程都必须在同一个锁上同步。
    volatile 变量：仅当 volatile 变量能简化代码的实现以及对同步策略的验证时，才应该使用它们。如果在验证正确性时需要
  对可见性进行复杂的判断，那么就不要使用 volatile 变量。volatile 变量的正确使用方式包括：确保他们自身状态的可见性，
  确保它们所引用对象的状态的可见性，以及标识一些重要的程序生命周期事件的发生(例如：初始化或关闭)。
    加锁机制既可以确保可见性又可以确保原子性，而 volatile 变量只能确保可见性。
  
  7.发布与逸出：
    “发布(Publish)”一个对象是指使对象能够在当前作用域之外的代码中使用。
    当某个不应该发布的对象被发布时，这种情况就被称为逸出(Escape)。
    安全对象的构造过程：不要在构造过程中使用 this 引用逸出。
  
  8.线程封闭：  
    Ad-hoc 线程封闭：维护线程封闭性的职责完全由程序实现来承担。
    栈封闭：线程封闭的一种特例，在栈封闭中，只能通过局部变量才能访问对象。
    ThreadLocal类：它能使线程中的某个值与保存值的对象关联起来。
  
  9.不变性：
    不可变的对象一定是线程安全的。
    当满足以下条件时，对象才是不可变的：
       (1) 对象创建以后其状态就不能修改；
       (2) 对象的所有域都是 final 类型；
       (3) 对象是正确创建的(在对象创建期间，this 引用没有逸出)。
    Final 域：正如“除非需要更高的可见性，否则应将所有的域都声明为私有域”是一个良好的编程习惯，“除非需要某个域是可变的，
  否则应将其声明为 final 域”也是一个良好的编程习惯。
    使用 volatile 类型来发布不可变的对象。
    
  10.安全发布：
     不正确的发布：正确的对象被破坏。
     不可变对象与初始化安全性：任何线程都可以在不需要额外同步的情况下安全地访问不可变对象，即使在发布这些对象时没有使用
  同步。
     安全发布的常用模式：要安全地发布一个对象，对象的引用以及对象的状态必须同时对其他线程可见。一个正确构造的对象可以
  通过以下方式来安全发布：
       (1) 在静态初始化函数中初始化一个对象引用；
       (2) 将对象的引用保存到 volatile 类型的域或者 AtomicReference 对象中；
       (3) 将对象的引用保存到某个正确构造对象的 final 类型域中；
       (4) 将对象的引用保存到一个由锁保护的域中。
       
     事实不可变对象：在没有额外的同步的情况下，任何线程都可以安全地使用被安全发布的事实不可变对象。
     
     可变对象：对象的发布需求取决它的可变性：
       (1) 不可变的对象可以通过任意机制来发布；
       (2) 事实不可变对象必须通过安全方式来发布；
       (3) 可变对象必须通过安全方式来发布，并且必须是线程安全的或者由某个锁保护起来。
       
     安全地共享对象：在并发程序中使用和共享对象时，可以使用一些实用的策略，包括：
       (1) 线程封闭：线程封闭的对象只能由一个线程拥有，对象被封闭在该线程中，并且只能由这个线程修改；
       (2) 只读共享：在没有额外同步的情况下，共享的只读对象可以由多个线程并发访问，但任何线程都不能修改它。
     共享的只读对象包括不可变对象和事实不可变对象；
       (3) 线程安全共享：线程安全的对象在其内部实现同步，因此多个线程可以通过对象的公有接口来进行访问而不需要进一步同步.
       (4) 保护对象：被保护的对象只能通过持有特定的锁来访问。保护对象包括封装在其他线程安全对象中的对象，以及已发布的
     并且由某个特定锁保护的对象。
     
  11.设计线程安全的类：
    在设计线程安全类的过程中，需要包含以下三个基本要素：
       (1) 找出构成对象状态的所有变量；
       (2) 找出约束状态变量的不可变性条件；
       (3) 建立对象状态的并发访问管理策略。
    
    收集同步需求：如果不了解对象的不变性条件与后验条件，那么就不能确保线程安全性。要满足在状态变量的有效值或状态转换
  上的各种约束条件，就需要借助于原子性与封装性。
  
  12.实例封闭：
    将数据封装在对象内部，可以将数据的访问限制在对象的方法上，从而更容易确保线程在访问数据时总能持有正确的锁。
    封闭机制更易于构造线程安全的类，因为当封闭类的状态，在分析类的线程安全性时就无须检查整个程序。
    
  13.线程安全性的委托：
    独立的状态变量。
    当委托失效时：如果一个类是由多个独立且线程安全的状态变量组成，并且在所有的操作中都不包含无效状态转换，那么可以
  将线程安全性委托给底层的状态变量。
    发布底层的状态变量：如果一个状态变量是线程安全的，并且没有任何不变性条件来约束它的值，在变量的操作上也不存在任何
  不允许的状态转换，那么就可以安全地发布这个变量。
  
  14.在现有的线程安全类中添加功能：
    (1) 客户端加锁机制：通过客户端加锁来实现“若没有则添加”。
    public class ListHelper<E> {
       public List<E> list = Collections.synchronizedList(new ArrayList<E>());
      
       public boolean putIfAbsent(E x) {
          synchronized (this) {
             boolean absent = ! list.contains(x);
             if (absent)
                list.add(x);
             return absent;
          }
       }
    }
    
    (2) 组合：通过组合实现“若没有则添加”。
    public class ImprovedList<T> implements List<T>  {
       private final List<T> list;
       
       public ImprovedList(List<T> list) { this.list = list; }
       
       public synchronized boolean putIfAbsent(T x) {
          boolean contains = list.contains(x);
          if (contains)
              list.add(x);
          return !contains;
       }
       
       public synchronized void clear() { list.clear(); }
       //按照类似的方式委托 List 的其他方法
    }
    
  15.将同步策略文档化：
    在文档中说明客户端代码需要了解的线程安全性保证，以及代码维护人员需要了解的同步策略。
  
  16.同步容器类：
    同步容器类包括 Vector 和 HashTable。
    迭代器与 ConcurrentModificationException。
    隐藏迭代器：正如封装对象的状态有助于维持不变性条件一样，封装对象的同步机制同样有助于确保实施同步策略。
  
  17.并发容器：
    通过并发容器来替代同步容器，可以极大地提高伸缩性并降低风险。
    ConcurrentHashMap。
    额外的原子 Map 操作：ConcurrentMap接口：
    public interface ConcurrentMap<K,V> extends Map<K,V> {
      //仅当 K 没有相应的映射值时才插入
      V putIfAbsent(K key, V value);
      
      //仅当 K 被映射到 V 时才移除
      boolean remove(K key, V value);
      
      //仅当 K 被映射到 oldValue 时才替换为 newValue
      boolean replace(K key, V oldValue, V newValue);
      
      //仅当 K 被映射到某个值时才替换为 newValue
      V replace(K key, V newValue);
    }
    CopyOnWriteArrayList用于替代同步 List。
    
  18.阻塞队列(BlockingQueue)和生产者-消费者模式： 
    在构建高可靠的应用程序时，有界队列是一种强大的资源管理工具：它们能抑制并防止产生过多的工作项，使应用程序在负荷过载的
  情况下变得更加健壮。
    串行线程封闭。
    双端队列与工作密取。
  
  19.阻塞方法与中断方法：传递 InterruptedException；恢复中断。
    public class TaskRunnable implements Runnable {
      BlockingQueue<Task> queue;
      
      public void run() {
        try {
           processTask(queue.take());
        } catch (InterruptedException e) {
           //恢复被中断的状态
           Thread.currentThread().interrupt();
        }
      }
    }
  
  20.同步工具类：
    同步工具类可以是任何一个对象，只要它根据其自身的状态来协调线程的控制流。阻塞队列可以作为同步工具类，
  其他类型的同步工具类还包括闭锁(Latch：CountDownLatch，FutureTask)、信号量(Semaphore)、
  栅栏(Barrier：CyclicBarrier，Exchanger)。
    
    (1) 在计时测试中使用 CountDownLatch 来启动和停止线程。
    public class TestHarness {
       public long timeTasks(int nThreads, final Runnable task) throws InterruptedException {
          final CountDownLatch startGate = new CountDownLatch(1);
          final CountDownLatch endGate = new CountDownLatch(nThreads);
          
          for(int i = 0; i < nThreads; i++) {
             Thread t = new Thread() {
                public void run() {
                   try {
                      startGate.await();
                      try { 
                         task.run();
                      } finally {
                         endGate.countDown();
                      }
                   } catch (InterruptedException ignored) { }
                }
             };
             t.start();
          }
          
          long start = System.nanoTime();
          startGate.countDown();
          endGate.await();
          long end = System.nanoTime();
          return end - start;
       }
    }
    
    (2) 使用 FutureTask 来提前加载稍后需要的数据。
    public class Preloader {
       private final FutureTask<ProductInfo> future = 
             new FutureTask<ProductInfo>(new Callable<ProductInfo>() {
                public ProductInfo call() throws DataLoadException {
                   return loadProductInfo();
                }
             });
       private final Thread thread = new Thread(future);
       
       public void start() { thread.start(); }
       
       public ProductInfo get() throws DataLoadException, InterruptedException {
          try {
             return future.get();
          } catch (ExecutionException e) {
             Throwable cause = e.getCause();
             if (cause instanceof DataLoadException)
                throw (DataLoadException) cause;
             else
                throw launderThrowable(cause);
          }
       }
    }
    
    (3) 使用 Semaphore 为容器设置边界。
    public class BoundedHashSet<T> {
       private final Set<T> set;
       private final Semaphore sem;
       
       public BoundedHashSet(int bound) {
          this.set = Collections.synchronizedSet(new HashSet<T>());
          sem = new Semaphore(bound);
       }
       
       public boolean add(T o) throws InterruptedException {
          sem.acquire();
          boolean wasAdded = false;
          try {
             wasAdded = set.add(o);
             return wasAdded;
          } finally {
             if (!wasAdded)
               sem.release();
          }
       }
       
       public boolean remove(Object o) {
          boolean wasRemoved = set.remove(o);
          if (wasRemoved)
             sem.release();
          return wasRemoved;
       }
    }
    
    (4) 通过 CyclicBarrier 协调自动细胞自动衍生系统中的计算。
    public class CellularAutomata {
       private final Board mainBoard;
       private final CyclicBarrier barrier;
       private final Worker[] workers;
       
       public CellularAutomata(Board board) {
          this.mainBoard = board;
          int count = Runtime.getRuntime().availableProcessors();
          this.barrier = new CyclicBarrier(count,
                 new Runnable() {
                    public void run() {
                       mainBoard.commitNewValues();
                    }});
          this.workers = new Worker[count];
          for (int i = 0; i < count; i++)
             workers[i] = new Worker(mainBoard.getSubBoard(count, i));
       }
       
       private class Worker implements Runnable {
          private final Board board;
          public Worker(Board board) { this.board = board; }
          public void run() {
             while(!board.hasConverged()) {
                for (int x = 0; x < board.getMaxX(); x++)
                  for (int y = 0; y < board.getMaxY(); y++)
                     board.setNewValue(x, y, computeValue(x, y));
                try {
                   barrier.await();
                } catch (InterruptedException ex) {
                   return;
                } catch (BrokenBarrierException ex) {
                   return;
                }
             }
          }
       }
       
       public void start() {
           for (int i = 0; i < workers.length; i++)
              new Thread(workers[i]).start();
           mainBoard.waitForConvergence();
       }
    }
    
 21.构建高效且可伸缩的结果缓存：
    public class Memoizer<A, V> implements Computable<A, V> {
       private final ConcurrentMap<A, Future<V>> cache = 
          new ConcurrentHashMap<A, Future<V>>();
       private final Computable<A, V> c;
       
       public Memoizer(Computable<A, V> c) { this.c = c; }
       
       public V compute(final A arg) throws InterruptedException {
          while (true) {
             Future<V> f = cache.get(arg);
             if (f == null) {
                Callable<V> eval = new  Callable<V>() {
                   public V call() throws InterruptedException {
                      return c.compute(arg);
                   }
                };
                FutureTask<V> ft = new FutureTask<V>(eval);
                f = cache.putIfAbsent(arg, ft);
                if (f == null) { f = ft; ft.run(); }
             }
             try {
                return f.get();
             } catch (CancellationException e) {
                cache.remove(arg, f);
             } catch (ExecutionException e) {
                throw launderThrowable(e.getCase());
             }
          }
       }
    }
```
