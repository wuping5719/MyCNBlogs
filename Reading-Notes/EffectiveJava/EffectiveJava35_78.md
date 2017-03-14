<h2>《Effective Java》 :books: </h2> 

> 布洛克 (Bloch, Joshua) 著    机械工业出版社

```html
35.注解优先于命名模式。
   (1) import java.lang.annotation.*;
       @Retention(RetentionPolicy.RUNTIME)
       @Target(ElementType.METHOD)
       public @interface Test {...}
   (2) import java.lang.annotation.*;
       @Retention(RetentionPolicy.RUNTIME)
       @Target(ElementType.METHOD)
       public @interface ExceptionTest {
           Class<? extends Exception> value();
       } 

36.坚持使用 Override 注解。
   (1) @Override public boolean equals(Object o) {
           if (!(o instanceof Bigram))
               return false;
           Bigram b = (Bigram) o;
           return b.first == first && b.second == second;
       }
    在想要覆盖超类声明的每个方法声明中使用 Override 注解。

37.用标记接口定义类型。
   标记接口(marker interface)是没有包含方法声明的接口。
   标记接口定义的类型是由被标记类的实例实现的；标记注解则没有定义这样的类型。
   如果在编码目标为 ElementType.Type 的标记注解类型，考虑标记接口是否更加合适。

38.检查参数的有效性。
   (1) public BigInteger mod(BigInteger m) {
          if (m.signum() <= 0)
             throw new ArthmeticException("Modulus <= 0:" + m);
       }
   (2) private static void sort(long a[], int offset, int length) {
          assert a != null;
          assert offset >= 0 && offset <= a.length;
          assert length >= 0 && length <= a.length - offset;
          ...
       }

39.必要时进行保护性拷贝。
   假设类的客户端会尽其所能来破坏这个类的约束条件，必须保护性地设计程序。
   对于构造器的每个可变参数进行保护性拷贝(defensive copy)。
   对于参数类型可以被不可信任方子类化的参数，请不要使用 clone 方法进行保护性拷贝。

40.谨慎设计方法签名。
  (1) 谨慎地选择方法的名称。
  (2) 不要过于追求提供便利的方法。
  (3) 避免过长的参数列表。
     缩短长参数列表的方法：
     ① 把方法分解成多个方法，每个方法只需要这些参数的一个子集。
     ② 创建辅助类(helper class)。
     ③ 从对象构建到方法调用都采用 Builder 模式。
  对于参数类型，要优先使用接口而不是类。
  对于 boolean 参数，要优先使用两个元素的枚举类型。

41.慎用重载。
   对于重载方法(overloaded method)的选择是静态的，而对于被覆盖的方法(overridden method)的选择是动态的。
   永远不要导出两个具有相同参数数目的重载方法。

42.慎用可变参数。
   static int min(int firstArg, int ... remainingArgs) {
      int min = firstArg;
      for (int arg : remainingArgs) 
         if (arg < min)
            min = arg;
      return min;
   }
   不必改造具有 final 数组参数的每个方法；只当确实是在数量不定的值上执行调用时才使用可变参数。

43.返回零长度的数组或者集合，而不是 null。

44.为所有导出的 API 元素编写文档注释。
   Javadoc 利用特殊格式的文档注释，根据源代码自动产生 API 文档。
   没有必要在文档注释中使用 HTML<code> 或 <tt> 标签了，Javadoc{@code}标签更好，它避免了转义 HTML 元字符。

45.将局部变量的作用域最小化。
   (1) 在第一次使用它的地方声明；
   (2) 几乎每个局部变量的声明都应该包含一个初始化表达式。
    for (int i = 0, n = expensiveComputation(); i < n; i++) { doSomething(i); }

46.for-each 循环优先于传统的 for 循环。
   for (Element e : elements) { doSomething(e); }
   for (Suit suit : suits) {
      for (Rank rank : ranks) {
          deck.add(new Card(suit, rank));
      }
   }
   三种常见情况无法使用 for-each 循环：
   (1) 过滤； (2) 转换； (3) 平行迭代。

47.了解和使用类库。
   (1) 使用标准类库，可以充分利用这些编写标准类库的专家的知识，以及在你之前其他人的使用经验。
   (2) 每个程序员都应该熟悉 java.lang、java.util 和 java.io 中的内容。

48.如果需要精确的答案，请避免使用 float 和 double。
   (1) float 和 double 类尤其不适合用于货币计算。
   (2) 使用 BigDecimal、int 或 long 进行货币计算。

49.基本类型优于装箱基本类型。
   (1) 对装箱基本类型运用 == 操作符几乎总是错误的。
     Comparator<Integer> naturalOrder = new Comparator<Integer>() {
         public int compare(Integer first, Integer second) {
            int f = first;
            int s = second;
            return f < s ? -1 : (f == s ? 0 : 1);
         }
     };
   (2) 当在一项操作中混合使用基本类型和装箱基本类型时，装箱基本类型就会自动拆箱。
   (3) 自动装箱减少了使用装箱基本类型的繁琐性，但是并没有减少它的风险。

50.如果其他类型更适合，则尽量避免使用字符串。
   (1) 字符串不适合代替其他的值类型；
   (2) 字符串不适合代替枚举类型；
   (3) 字符串不适合代替聚集类型；
   (4) 字符串不适合代替能力表。
     public final class ThreadLocal<T> {
         public ThreadLocal() {}
         public void set(T value);
         public T get();
     }
     
51.当心字符串连接的性能。
  (1) 为连接 n 个字符串而重复地使用字符串连接操作符，需要 n 的平方级的时间；
  (2) 为了获得可以接受的性能，请使用 StringBuilder 替代 String。

52.通过接口引用对象。
  (1) 如果有合适的接口类型存在，对于参数、返回值、变量和域来说，都应该使用接口类型进行声明。
  (2) List<Subscriber> subscriber = new Vector<Subscriber>();
  (3) 如果没有合适的接口存在，完全可以用类而不是接口来引用对象。

53.接口优先于反射机制。
  (1) 核心反射机制(core reflection facility) java.lang.reflect, 提供了"通过程序来访问关于已装载的类的信息"
的能力。
  代价：① 丧失了编译时类型检查的好处；
      ② 执行反射机制访问所需要的代码非常笨拙和冗长；
      ③ 性能损失。
  (2) 可以以反射方式创建实例，然后通过它们的接口或者超类，以正常的方式访问这些实例。

54.谨慎地使用本地方法。
  Java Native Interface (JNI) 允许 Java 应用程序可以调用本地方法(native method)。
   ① 访问特定平台的机制(如访问注册表和文件锁)；
   ② 访问遗留代码库的能力；
   ③ 编写应用程序中注重性能的部分。

55.谨慎地进行优化。
  (1) 努力避免那些限制性能的设计决策。
  (2) 要考虑 API 设计决策的性能后果。

56.遵守普遍接受的命名惯例。

57.只针对异常的情况才使用异常。

58.对可恢复的情况使用受检异常，对编程错误使用运行时异常。

59.避免不必要地使用受检的异常。

60.优先使用标准的异常。
  IllegalArgumentException：非 null 的参数值不正确；
  IllegalStateException：对于方法调用而言，对象状态不合适；
  NullPointerException：在禁止使用 null 的情况下参数为 null；
  IndexOutOfBoundsException：下标参数值越界；
  ConcurrentModificationException：在禁止并发修改的情况下，检测到对象的并发修改；
  UnsupportedOperationException：对象不支持用户请求的方法。

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
