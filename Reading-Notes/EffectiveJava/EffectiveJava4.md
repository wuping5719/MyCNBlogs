<h2>《Effective Java》 :books: </h2> 

> 布洛克 (Bloch, Joshua) 著    机械工业出版社

```java
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
```
