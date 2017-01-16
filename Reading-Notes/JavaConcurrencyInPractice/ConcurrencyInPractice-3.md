<h2>《Java 并发编程实战》 :books: </h2> 
> Brian Goetz、 Tim Peierls、 Joshua Bloch、 Joseph Bowbeer、 David Holmes、 Doug Lea 著   

```html
  35.避免活跃性危险：
  活跃性故障是一个非常严重的问题，因为当出现活跃性故障时，除了中止应用程序之外没有其他任何机制可以帮助从这种故障中恢复过来
  最常见的活跃性故障就是锁顺序死锁。在设计时应避免产生锁顺序死锁：确保线程在获取多个锁时采用一致的顺序。
  最好的解决方法是在程序中始终使用开放调用。这将大大减少需要同时持有多个锁的地方，也更容易发现这些地方。
  
  36.死锁：
   (1) 锁顺序死锁：如果所有线程以固定的顺序来获得锁，那么在程序中就不会出现锁顺序死锁问题。
   (2) 动态的锁顺序死锁：
     通过锁顺序来避免死锁：
    private static final Object tieLock = new Object();
    public void transferMoney(final Account fromAcct, final Account toAcct, 
                    final DollarAmount amount) throws InsufficientFundsException {
       class Helper {
          public void transfer() throws InsufficientFundsException {
             if (fromAcct.getBalance().compareTo(amount) < 0)
                throw new InsufficientFundsException();
             else {
                fromAcct.debit(amount);
                toAcct.credit(amount);
             }
          }
       }
       int fromHash = System.identityHashCode(fromAcct);
       int toHash = System.identityHashCode(toAcct);

       if (fromHash < toHash) {
          synchronized (fromAcct) {
             synchronized (toAcct) {
                new Helper().transfer();
             }
          }
       } else if (fromHash > toHash) {
          synchronized (toAcct) {
             synchronized (fromAcct) {
                new Helper().transfer();
             }
          }
       } else {
          synchronized (tieLock) {
             synchronized (fromAcct) {
                synchronized (toAcct) {
                   new Helper().transfer();
                }
             }
          }
       }
    }
    
  (3) 在协作对象之间发生的死锁：
    如果在持有锁时调用某个外部方法，那么将出现活跃性问题。在这个外部方法中可能会获取其他锁(这可能会产生死锁)，
  或者阻塞时间过长，导致其他线程无法及时获得当前被持有的锁。
  (4) 开放调用：
    在程序中应尽量使用开放调用。与那些在持有锁时调用外部方法的程序相比，更易于对依赖于开放调用的程序进行死锁分析。
  (5) 资源死锁。

 37.死锁的避免与诊断：支持定时的锁；通过线程转储信息来分析死锁。
 
 38.其他活跃性危险：
  (1) 饥饿：要避免使用线程优先级，因为这会增加平台依赖性，并可能导致活跃性问题。在大多数并发应用程序中，
 都可以使用默认的线程优先级。
  (2) 糟糕的响应性。
  (3) 活锁(Livelock)。
  
 39.性能与可伸缩性：
    由于使用线程常常是为了充分利用多个处理器的计算能力，因此在并发应用程序性能的讨论中，通常更多地将侧重点放在吞吐量
 和可伸缩性上，而不是服务时间。Amdahl 定律告诉我们，程序的可伸缩性取决于所在代码中必须被串行执行的代码比例。因为 Java
 程序中串行操作的主要来源是独占方式的资源锁，因此通常可以通过以下方式来提升可伸缩性：减少锁的持有时间，降低锁的粒度，
 以及采用独占的锁或非阻塞锁来代替独占锁。
 
 40.对性能的思考：
  (1) 性能与可伸缩性：
   可伸缩性指的是：当增加计算资源(例如：CPU、内存、存储容量或I/O带宽)时，程序的吞吐量或者处理能力能相应地增加。
  (2) 评估各种性能权衡因素：
   避免不成熟的优化。首先使程序正确，然后再提高运行速度——如果它还运行得不够快。
   以测试为基准，不要猜测。

 41.Amdahl 定律：假定 F 是必须被串行执行的部分，那么根据 Amdahl 定律，在包含 N 个处理器的机器中，
   最高的加速比为：Speedup ≦ 1 / (F + (1 - F) / N). 
   当 N 趋近无穷大时，最大的加速比趋近于 1/F。
   在所有并发程序中都包含一些串行部分。如果你认为在你的程序中不存在串行部分，那么可以再仔细检查一遍。

 42.线程引入的开销：
  (1) 上下文切换。
  (2) 内存同步：不要过度担心非竞争同步带来的开销。这个基本的机制已经非常快了，并且 JVM 还能进行额外的优化以进一步
 降低或取消开销。因此，我们应该将优化重点放在那些发生锁竞争的地方。
  (3) 阻塞。
  
 43.减少锁的竞争：
  在并发程序中，对可伸缩性的最主要威胁就是独占方式的资源锁。
  有3种方式可以降低锁的竞争程度：
    减少锁的持有时间；
    降低锁的请求频率；
    使用带有协调机制的独占锁，这些机制允许更高的并发性。
  (1) 缩小锁的范围(“快进快出”)。
    减小锁的持有时间:
    public class BetterAttributeStore {
       private final Map<String, String> attributes = new HashMap<String, String>();

       public boolean userLocationMatches(String name, String regexp) {
          String key = "users." + name + ".location";
          String location;
          synchronized (this) {
             location = attributes.get(key);
          }
          if (location == null)
             return false;
          else
             return Pattern.matches(regexp, location);
       }
    }

  (2) 减小锁的粒度。
    将 ServerStatus 重新改写为使用锁分解技术：
    public class ServerStatus {
       public final Set<String> users;
       public final Set<String> queries;
       ...
       public void addUser(String u) {
          synchronized (users) {
             users.add(u);
          }
       }

       public void addQuery(String q) {
          synchronized (queries) {
             queries.add(q);
          }
       }
       // 去掉同样被改写为使用被分解锁的方法
    }

  (3) 锁分段。
    在基于散列的 Map 中使用锁分段技术：
    public class StripedMap {
       // 同步策略：buckets[n] 由 locks[n % N_LOCKS] 来保护
       private static final int N_LOCKS = 16;
       private final Node[] buckets;
       private final Object[] locks;

       private static class Node { ... }

       public StripedMap(int numBuckets) {
          buckets = new Node[numBuckets];
          locks = new Object[N_LOCKS];
          for(int i = 0; i < N_LOCKS; i++)
             locks[i] = new Object();
       }

       private final int hash(Object key) {
          return Math.abs(key.hashCode() % buckets.length);
       }

       public Object get(Object key) {
          int hash = hash(key);
          synchronized (locks[hash % N_LOCKS]) {
             for(Node m = buckets[hash]; m != null; m = m.next)
                if (m.key.equals(key))
                   return m.value;
          }
          return null;
       }

       public void clear() {
          for(int i = 0; i < buckets.length; i++) {
             synchronized (locks[i % N_LOCKS]) {
                buckets[i] = null;
             }
          }
       }
       ...
    }

  (4) 避免热点域。
  (5) 一些替代独占锁的方法：ReadWriteLock、原子变量。
  (6) 监控 CPU 的利用率：负载不充足；I/O密集；外部限制；锁竞争。
  (7) 向对象池说“不”：通常，对象分配操作的开销比同步开销更低。
  (8) 减少上下文切换的开销。
  
 44.并发程序的测试：
   要测试并发程序的正确性可能非常困难，因为并发程序的许多故障模式都是一些低概率事件，它们对于执行时序、负载情况以及
其他难以重现的条件都非常敏感。而且，在测试程序中还会引入额外的同步或执行时序限制，这些因素将掩盖被测试代码中的一些
并发问题。要测试并发程序的性能同样非常困难，与使用静态编译语言(例如C)编写的程序相比，用Java编写的程序在测试起来更加
困难，因为动态编译、垃圾回收以及自动优化等操作都会影响与时间相关的测试结果。
   要想尽可能地发现潜在的错误以及避免它们在正式产品中暴露出来，我们需要将传统的测试技术与代码审查和自动化分析工具结合
起来，每项技术都可以找出其他技术忽略的问题。

```
