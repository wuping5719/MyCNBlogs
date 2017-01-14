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
```
