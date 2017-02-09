<h2>《Java 并发编程实战》 :books: </h2> 
> Brian Goetz、 Tim Peierls、 Joshua Bloch、 Joseph Bowbeer、 David Holmes、 Doug Lea 著   

```html
 49.显式锁：
   与内置锁相比，显式的 Lock 提供了一些扩展功能，在处理锁的不可用性方面有着更高的灵活性，并且对队列行有着更好的控制。
但 ReentrantLock 不能完全替代 synchronized，只有在 synchronized 无法满足需求时，才应该使用它。
   读-写锁允许多个读线程并发地访问被保护的对象，当访问以读取操作为主的数据结构时，它能提高程序的可伸缩性。
   
 50.Lock 与 ReentrantLock：
   (1) Lock 接口：
   public interface Lock {
      void lock();
      void lockInterruptibly() throws InterruptedException;
      boolean tryLock();
      boolean tryLock(long timeout, TimeUnit unit) throws InterruptedException;
      void unlock();
      Condition newCondition();
   }
   
   (2) 轮询锁与定时锁：可定时的与可轮询的锁获取模式是由 tryLock 方法实现的，与无条件的锁获取模式相比，它具有更完善的
  错误恢复机制。
    通过 tryLock 来避免锁顺序死锁：
    public boolean transferMoney(Account fromAcct, Account toAcct, DollarAmount amount, 
                  long timeout, TimeUnit unit) throws InsufficientFundsException, InterruptedException {
        long fixedDelay = getFixedDelayComponentNanos(timeout, unit);
        long randMod = getRandomDelayModulusNanos(timeout, unit);   
        long stopTime = System.nanoTime() + unit.toNanos(timeout);

        while (true) {
           if (fromAcct.lock.tryLock()) {
              try {
                 if (toAcct.lock.tryLock()) {
                    try {
                       if (fromAcct.getBalance().compareTo(amount) < 0)
                          throw new InsufficientFundsException();
                       else {
                          fromAcct.debit(amount);
                          toAcct.credit(amount);
                          return true;
                       }
                    } finally {
                       toAcct.lock.unlock();
                    }
                 }
              } finally {
                 fromAcct.lock.unlock();
              }
           }
           if(System.nanoTime() < stopTime)
              return false;
           NANOSECONDS.sleep(fixedDelay + rnd.nextLong() % randMod);
        }
    }

  (3) 可中断的锁获取操作：
    public boolean sendOnSharedLine(String message) throws InterruptedException {
       lock.lockInterruptibly();
       try {
          return cancellableSendOnSharedLine(message);
       } finally {
          lock.unlock();
       }
    }

    private boolean cancellableSendOnSharedLine(String message) throws InterruptedException { ... }

 (4) 非块结构的加锁。
 
 51.性能考虑因素：性能是一个不断变化的指标，如果在昨天的测试基准中发现 X 比 Y 更快，那么在今天就可能已经过时了。
  公平性：在 ReentrantLock 的构造函数中提供了两种公平性选择：创建一个非公平的锁(默认)或者一个公平的锁。
  在 synchronized 和 ReentrantLock 之间进行选择：在一些内置锁无法满足需求的情况下，ReentrantLock 可以作为
一种高级工具。当需要一些高级功能时才应该使用 ReentrantLock，这些功能包括：可定时的、可轮询的与可中断的锁获取
操作，公平队列，以及非块结构的锁。否则，还是应该优先使用 synchronized。

 52.读-写锁：
  ReadWriteLock 接口：
  public interface ReadWriteLock {
     Lock readLock();
     Lock writeLock();
  }
  ReadWriteLock 中的一些可选实现包括：释放优先；读线程插队；重入性；降级；升级。
  用读—写锁来包装 Map:
  public class ReadWriteMap<K, V> {
     private final Map<K, V> map;
     private final ReadWriteLock lock = new ReentrantReadWriteLock();
     private final Lock r = lock.readLock();
     private final Lock w = lock.writeLock();

     public ReadWriteMap(Map<K, V> map) {
        this.map = map;
     }

     public V put(K key, V value) {
        w.lock();
        try {
           return map.put(key, value);
        } finally {
           w.unlock();
        }
     }
     // 对 remove(), putAll(), clear() 等方法执行相同的操作

     public V get(Object key) {
        r.lock();
        try {
           return map.get(key);
        } finally {
           r.unlock();
        }
     }
     // 对其他只读的 Map 方法执行相同的操作
  }
  
 53.构建自定义的同步工具：
  要实现一个依赖状态的类——如果没有满足依赖状态的前提条件，那么这个类的方法必须阻塞，那么最好的方式是基于现有的
库类来构建，例如 Semaphore.BlockingQueue 或 CountDownLatch。然而，有时候现有的库类不能提供足够的功能，在这种
情况下，可以使用内置的条件队列、显示的 Condition 对象或者 AbstractQueuedSynchronizer 来构建自己的同步器。
内置条件队列与内置锁是紧密绑定在一起的，这是因为管理状态依赖性的机制必须与确保状态一致性的机制关联起来。同样，
显示的 Condition 与显示的 Lock 也是紧密地绑定到一起的，并且与内置条件队列相比，还提供了一个扩展的功能集，包括
每个锁对应于多个等待线程集，可中断或不可中断的条件等待，公平或非公平的队列操作，以及基于时限的等待。

 54.原子变量与非阻塞同步机制：
  非阻塞算法通过底层的并发原语(例如比较并交换而不是锁)来维持线程的安全性。这些底层的原语通过原子变量类向外公开，
这些类也用做一种“更好的 volatile 变量”，从而为整数和对象引用提供原子的更新操作。
  非阻塞算法在设计和实现时非常困难，但通常能够提供更高的伸缩性，并能够更好的防止活跃性故障的发生。在 JVM 从一个
版本升级到下一个版本的过程中，并发性能的主要提升都来自于(在 JVM 内部以及平台类库中)对阻塞算法的使用。

 55.条件队列：
 1) 使用条件队列实现的有界缓存：
  public class BoundedBuffer<V> extends BaseBoundedBuffer<V> {
    // 条件谓词：not-full (!isFull())
    // 条件谓词：not-empty (!isEmpty())

    public BoundedBuffer(int size) { super(size); }

    // 阻塞并直到：not-full 
    public synchronized void put(V v) throws InterruptedException {
       while (isFull()) 
          wait();
       doPut(v);
       notifyAll();
    }

    // 阻塞并直到：not-empty
    public synchronized V take() throws InterruptedException {
       while (isEmpty()) 
          wait();
       V v = doTake();
       notifyAll();
       return v;
    }
  }
  
 2) 条件谓词：
   将与条件队列相关联的条件谓词以及这些条件谓词上等待的操作都写入文档。
   每一次 wait 调用都会隐式地与特定的条件谓词关联起来。当调用某个特定条件谓词的 wait 时，调用者必须已经持有
 与条件队列相关的锁，并且这个锁必须保护着构成条件谓词的状态变量。
 3) 过早唤醒：  
   状态依赖方法的标准形式：
   void stateDependentMethod() throws InterruptedException {
      // 必须通过一个锁来保护条件谓词
      synchronized(lock) {
         while (!conditionPredicate())
            lock.wait();
         // 现在对象处于合适的状态
      }
   }
   当使用条件等待时(例如 Object.wait 或 Condition.await)：
    (1) 通常都有一个条件谓词——包括一些对象状态的测试，线程在执行前必须首先通过这些测试。
    (2) 在调用 wait 之前测试条件谓词，并且从 wait 中返回时再次进行测试。
    (3) 在一个循环中调用 wait。
    (4) 确保使用与条件队列相关的锁来保护构成条件谓词的各个状态变量。
    (5) 当调用 wait、notify 或 notifyAll 等方法时，一定要持有与条件队列相关的锁。
    (6) 在检查条件谓词之后以及开始执行相应的操作之前，不要释放锁。
  4) 丢失的信号。
  5) 通知：每当在等待一个条件时，一定要确保在条件谓词变为真时通过某种方式发出通知。
     只有同时满足以下两个条件时，才能用单一的 notify 而不是 notifyAll：
     所有等待线程的类型都相同。只有一个条件谓词与条件队列相关，并且每个线程在从 wait 返回后将执行相同的操作。
     单进单出。在条件变量上的每次通知，最多只能唤醒一个线程来执行。
  6) 阀门类：使用 wait 和 notifyAll 来实现可重新关闭的阀门。
    public class ThreadGate {
       // 条件谓词：opened-since(n) (isOpen || generation>n)
       @GuardedBy("this") private boolean isOpen;
       @GuardedBy("this") private int generation;

       public synchronized void close() {
          isOpen = false;
       }

       public synchronized void open() {
          ++generation;
          isOpen = true;
          notifyAll();
       }

       // 阻塞并直到：opened-since(generation on entry) 
       public synchronized void await() throws InterruptedException {
          int arrivalGeneration = generation;
          while(!isOpen && arrivalGeneration == generation)
             wait();
       }
    } 
  7) 子类的安全问题。
  8) 封装条件队列。
  9) 人口协议与出口协议。

 56.显示的 Condition 对象：
 (1) Condition 接口：
 public interface Condition {
    void await() throws InterruptedException;
    boolean await(long time, TimeUnit unit) throws InterruptedException;
    long awaitNanos(long nanosTimeout) throws InterruptedException;
    void awaitUninterruptibly();
    boolean awaitUntil(Date deadline) throws InterruptedException;

    void signal();
    void signalAll();
 }
   特别注意：在 Condition 对象中，与 wait、notify 和 notifyAll 方法对应的分别是await、signal 和 signalAll。
 但是，Condition 对 Object 进行了扩展，因而它也包含 wait 和 notify 方法。一定要确保使用正确的版本——await 和signal.
 (2) 使用显示条件变量的有界缓存：
 public class ConditionBoundedBuffer<T> {
    protected final Lock lock = new ReentrantLock();
    // 条件谓词：notFull (count < items.length)
    private final Condition notFull = lock.newCondition();
    // 条件谓词：notEmpty (count > 0) 
    private final Condition notEmpty = lock.newCondition();
    @GuardBy("lock") private final T[] items = (T[]) new Object[BUFFER_SIZE];
    @GuardBy("lock") private int tail, head, count;

    // 阻塞并直到：notFull
    public void put(T x) throws InterruptedException {
       lock.lock();
       try {
          while (count == items.length)
              notFull.await();
          items[tail] = x;
          if (++tail == items.length)
             tail = 0;
          ++count;
          notEmpty.signal();
       } finally {
          lock.unlock();
       }
    }

    // 阻塞并直到：notEmpty
    public T take() throws InterruptedException {
       lock.lock();
       try {
          while (count == 0)
            notEmpty.await();
          T x = items[head];
          items[head] = null;
          if (++head == items.length)
             head = 0;
          --count;
          notFull.signal();
          return x;
       } finally {
          lock.unlock();
       }
    }
 }

 57.AbstractQueuedSynchronizer：AQS 中获取操作和释放操作的标准形式。
  boolean acquire() throws InterruptedException {
     while (当前状态不允许获取操作) {
        if (需要阻塞获取请求) {
           如果当前线程不在队列中，则将其插入队列
           阻塞当前线程
        }
        else
           返回失败
     }
     可能更新同步器的状态
     如果线程位于队列中，则将其移出队列
     返回成功
  }

  void release() {
     更新同步器的状态
     if (新的状态允许某个被阻塞的线程获取成功)
        解除队列中一个或多个线程的阻塞状态
  }
  
 58.硬件对并发的支持：
  (1) 比较并交换(CAS)：模拟 CAS 操作。
  public class SimulatedCAS {
     @GuardedBy("this") private int value;

     public synchronized int get() { return value; }

     public synchronized int compareAndSwap(int expectedValue, int newValue) { 
        int oldValue = value;
        if (oldValue == expectedValue)
           value = newValue;
        return oldValue;
     }

     public synchronized boolean compareAndSet(int expectedValue, int newValue) { 
        return (expectedValue == compareAndSwap(expectedValue, newValue));
     }
  }

  (2) 非阻塞的计数器：基于 CAS 实现的非阻塞计数器。
  public class CasCounter {
     private SimulateCAS value;

     public int getValue() {
        return value.get();
     }
    
     public int increment() {
        int v;
        do {
           v = value.get();
        }
        while (v != value.compareAndSwap(v, v + 1));
        return v + 1;
     }
  }
  (3) JVM 对 CAS 的支持。

 59.原子变量类：
  1) 原子变量是一种“更好的 volatile”。
  2) 性能比较：锁与原子变量。
  (1) 基于 ReentrantLock 实现的随机数生成器。
  public class ReentrantLockPaseudoRandom extends PaseudoRandom {
     private final Lock lock = new ReentrantLock(false);
     private int seed;

     ReentrantLockPaseudoRandom(int seed) {
        this.seed = seed;
     }

     public int nextInt(int n) {
        lock.lock();
        try {
           int s = seed;
           seed = calculateNext(s);
           int remainder = s % n;
           return remainder > 0 ? remainder : remainder + n;
        } finally {
           lock.unlock();
        }
     }
  }
  (2) 基于 AtomicInteger 实现的随机数生成器。
  public class AtomicPaseudoRandom extends PaseudoRandom {
     private AtomicInteger seed;

     AtomicPaseudoRandom(int seed) {
        this.seed = new AtomicInteger(seed);
     }

     public int nextInt(int n) {
        while (true) {
           int s = seed.get();
           int nextSeed = calculateNext(s);
           if (seed.compareAndSet(s, nextSeed)) {
               int remainder = s % n;
               return remainder > 0 ? remainder : remainder + n;
           }
        }
     }
  }

 60.非阻塞算法：
  (1) 非阻塞的栈：使用 Treiber 算法(Treiber,1986)构造的非阻塞栈。
  public class ConcurrentStack <E> {
     AtomicReference<Node<E>> top = new AtomicReference<Node<E>>();

     public void push(E item) {
        Node<E> newHead = new Node<E>(item);
        Node<E> oldHead;
        do {
           oldHead = top.get();
           newHead.next = oldHead;
        } while (!top.compareAndSet(oldHead, newHead));
     }

     public E pop() {
        Node<E> oldHead;
        Node<E> newHead;
        do {
           oldHead = top.get();
           if (oldHead == null)
              return null;
           newHead = oldHead.next;
        } while (!top.compareAndSet(oldHead, newHead));
        return oldHead.item;
     }

     private static class Node<E> {
         public final E item;
         public Node<E> next;

         public Node(E item) {
            this.item = item;
         }
     }
  }

  (2) 非阻塞的链表：Michael-Scott(Michael  and Scott,1996)非阻塞算法中的插入算法。
  public class LinkedQueue <E> {
     private static class Node <E> {
        final E item;
        final AtomicReference<Node<E>> next;

        public Node(E item, Node<E> next) {
           this.item = item;
           this.next = new AtomicReference<Node<E>>(next);
        }
     }

     private final Node<E> dummy = new Node<E>(null, null);
     private final AtomicReference<Node<E>> head = new AtomicReference<Node<E>>(dummy);
     private final AtomicReference<Node<E>> tail = new AtomicReference<Node<E>>(dummy);

     public boolean put(E item) {
        Node<E> newNode = new Node<E>(item, null);
        while (true) {
           Node<E> curTail = tail.get();
           Node<E> tailNext = curTail.next.get();
           if (curTail == tail.get()) {
              if (tailNext != null) {
                 // 队列处于中间状态，推进尾节点
                 tail.compareAndSet(curTail, tailNext);
              } else {
                 // 处于稳定状态，尝试插入新节点
                 if (curTail.next.compareAndSet(null, newNode)) {
                    // 插入操作成功，尝试推进尾节点
                    tail.compareAndSet(curTail, newNode);
                    return true;
                 }
              }
           }
        }
     }
  }
  (3) 原子的域更新器。
  (4) ABA 问题。
```
