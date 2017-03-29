<h2>《Effective Java》 :books: </h2> 

> 布洛克 (Bloch, Joshua) 著    机械工业出版社

```java
61.抛出与抽象相对应的异常。
  更高层的实现应该捕获低层的异常，同时抛出可以按照高层抽象进行解释的异常(异常转译)。
  public E get(int index) {
     ListIterator <E> i = listIterator(index);
     try {
         return i.next();
     } catch (NoSuchElementException e) {
         throw new IndexOutOfBoundsException("Index: " + index);
     }
  }
  尽管异常转译与不加选择地从低层传递异常的做法相比有所改进，但是它也不能被滥用。

62.每个方法抛出的异常都要有文档。
  (1) 始终要单独地声明受检的异常，并且利用 Javadoc 的 @throws 标记，准确地记录下抛出每个异常的条件。
  (2) 使用 Javadoc 的 @throws 标签记录下一个方法可能抛出的每个未受检异常，但是不要使用 throws 关键字
将未受检的异常包含在方法的声明中。
  (3) 如果一个类中的许多方法出于同样的原因而抛出同一个异常，在该类的文档注释中对这个异常建立文档，这是可以接受的。

63.在细节消息中包含能捕获失败的信息。
  为了捕获失败，异常的细节信息应该包含所有"对该异常有贡献"的参数和域的值。

64.努力使失败保持原子性。
  失败原子性：失败的方法调用应该使对象保持在被调用之前的状态。

65.不要忽略异常。

66.同步访问共享的可变数据。
   public class StopThread {
      private static boolean stopRequested;
      private static synchronized void requestStop() {
          stopRequested = true;
      }
      private static synchronized boolean stopRequested() {
          return stopRequested;
      }
      public static void main(String[] args) throws InterruptedException {
          Thread backgroundThread = new Thread(new Runnable() {
             public void run() {
                int i = 0;
                while(!stopRequested())
                   i++;
             }
          });
          backgroundThread.start();
          TimeUnit.SECONDS.sleep(1);
          requestStop();
      }
   }
   当多个线程共享可变数据的时候，每个读或写数据的线程都必须执行同步。

67.避免过度同步。

68.executor 和 task 优先于线程。
   (1) ExecutorService executor = Executors.newSingleThreadExecutor();
       executor.execute(runnable);
       executor.shutdown();
   (2) 任务有两种：Runnable 及 Callable。

69.并发工具优先于 wait 和 notify。
   java.util.concurrent 中更高级的工具分成三类：Executor Framework、并发集合(Concurrent Collection)
以及同步器(Synchronizer)。
   (1) 并发集合：ConcurrentHashMap;
   (2) 同步器：Countdown Latch(倒计数锁存器), Semaphore, CyclicBarrier 和 Exchanger。
   始终应该使用 wait 循环模式来调用 wait 方法；永远不要在循环之外调用 wait 方法。

70.线程安全性的文档化。
   一个类为了可被多个线程安全地使用，必须在文档中清楚地说明它所支持的线程安全性级别。
   线程安全性级别：
     ① 不可变的(immutable);
     ② 无条件的线程安全(unconditionally thread-safe);
     ③ 有条件的线程安全;
     ④ 非线程安全(not thread-safe);
     ⑤ 线程对立(thread-hostile)。

71.慎用延迟初始化。
   在大多数情况下，正常初始化要优先于延迟初始化。
      private final FieldType field = computeFieldValue();
   (1) 如果利用延迟初始化来破坏初始化的循环，就要使用同步访问方法。
      private FieldType field;
      synchronized FieldType getField() {
          if (field == null) 
             field = computeFieldValue();
          return field;
      }
   (2) 如果出于性能的考虑而需要对静态域使用延迟初始化，就使用 lazy initialization holder class 模式。
   (3) 如果出于性能的考虑而需要对实例域使用延迟初始化，就使用双重检查模式。
      private volatile FieldType field;
      FieldType getField() {
         FieldType result = field;
         if (result == null) {
            synchronized(this) {
                result = field;
                if (result == null)
                   field = result = computeFieldValue();
            }
         }
         return result;
      }
    对于实例域，使用双重检查模式(duoble-check idiom); 对于静态域，则使用 lazy initialization 
holder class idiom; 对于可接受重复初始化的实例域，可以考虑使用单重检查模式。

72.不要依赖于线程调度器。
   (1) 可运行线程的平均数量不明显多于处理器数量。
   (2) 不要企图通过调用 Thread.yield 来"修正"该程序，Thread.yield 没有可测试的语义。
   (3) 线程优先级是 Java 平台上最不可移植的特征。
   (4) Thread.yield 的唯一用途是在测试期间人为地增加程序的并发性。
   应该使用 Thread.sleep(1) 代替 Thread.yield 来进行并发测试。

73.避免使用线程组。

74.谨慎地实现 Serializable 接口。
   (1) 实现 Serializable 接口而付出的最大代价是：一旦一个类被发布，就大大降低了"改变这个类的实现"的灵活性。
   流的唯一标识符(序列版本 UID (serial version UID))。
   (2) 实现 Serializable 的第二个代价是：它增加了出现 Bug 和安全漏洞的可能性。
   (3) 实现 Serializable 的第三个代价是：随着类发行新的版本，相关的测试负担也增加了。
   内部类不应该实现 Serializable，静态成员类可以实现 Serializable 接口。

75.考虑使用自定义的序列化形式。
   (1) 如果没有先认真考虑默认的序列化形式是否合适，则不要贸然接受。
   (2) 如果一个对象的物理表示法等同于它的逻辑内容，可能就适合于使用默认的序列化形式。
   (3) 即使确定了默认的序列化形式是否合适的，通常还必须提供一个 readObject 方法以保证约束关系和安全性。
   (4) 当一个对象的物理表示法与它的逻辑数据内容有实质性的区别时，使用默认序列化形式有以下 4 个缺点：
     ① 它使这个类的导出 API 永远地束缚在该类的内部表示法上；
     ② 它会消耗过多的空间；
     ③ 它会消耗过多的时间；
     ④ 它会引起栈溢出。
   (5) 在决定将一个域做成非 transient (瞬时的) 的前，请一定要确信它的值将是该对象的逻辑状态的一部分。
   (6) 如果在读取整个对象状态的任何其他方法上强制任何同步，则也必须在对象序列化上强制这种同步。
   (7) 为自己编写的每个可序列化的类声明一个显示的序列版本 UID。
      priavte static final long serialVersionUID = randomLongValue;

76.保护性地编写 readObject 方法。
   private void readObject(ObjectInputStream s) throws IOException, ClassNotFoundException {
       s.defaunltReadObject();
       start = new Date(start.getTime());
       end = new Date(end.getTime());
       if (start.compareTo(end) > 0)
           throw new InvalidObjectException(start + " after " + end);
   }

77.对于实例控制，枚举类型优先于 readResolve。
   如果依赖 readResolve 进行实例控制，则带有对象引用类型的所有实例域都必须声明为 transient 的。

78.考虑用序列化代理代替序列化实例。
   private static Class SerializationProxy <E extends Enum<E>> implements Serializable {
       private final Class<E> elementType;
       private final Enum[] elements;
       SerializationProxy(EnumSet<E> set) {
          elementType = set.elementType;
          elements = set.toArray(EMPTY_ENUM_ARRAY);
       }
       private Object readResolve() {
          EnumSet<E> result = EnumSet.noneof(elementType);
          for (Enum e : elements)
             result.add((E) e);
          return result;
       }
       private static final long serialVersionUID = 36216789L;
   }
```
