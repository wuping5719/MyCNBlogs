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
```
