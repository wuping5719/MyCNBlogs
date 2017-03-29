<h2>《Effective Java》 :books: </h2> 

> 布洛克 (Bloch, Joshua) 著    机械工业出版社

```java
31.用实例域代替序数。
   永远不要根据枚举的序数导出与它关联的值，而是要将它保存在一个实例域中。
   public enum Ensemble {
      SOLO(1), DUET(2), TRIO(3), QUARTET(4), QUINTET(5),
      SEXTET(6), SEPTET(7), OCTET(8), DOUBLE_QUARTET(8),
      NONET(9), DECTET(10), TRIPLE_QUARTET(12);
      private final int numberOfMusicians;
      Ensemble(int size) { 
         this.numberOfMusicians = size;
      }
      public int numberOfMusicians() {
         return numberOfMusicians;
      }
   }

32.用 EnumSet 代替位域。
   因为枚举类型要用在集合(Set)中，所以没理由用位域表示它。
   public class Text {
      public enum Style {
         BOLD, ITALIC, UNDERLINE, STRIKETHROUGH
      }
      public void applyStyles(Set<Style> styles) {...}
   }
   Text.applyStyles(EnumSet.of(Style.BOLD, Style.ITALIC));

33.用 EnumMap 代替序数索引。
   Map<Herb.Type, Set<Herb>> herbsByType = new EnumMap<Herb.Type, Set<Herb>>(Herb.Type.class);
   for (Herb.Type t: Herb.Type.values())
      herbsByType.put(t, new HashSet<Herb>());
   for (Herb h: garden)
      herbsByType.get(h.type).add(h);
   System.out.println(herbsByType);

34.用接口模拟可伸缩的枚举。
   (1) public interface Operation {
          double apply(double x, double y);
       }
       public enum ExtendedOperation implements Operation {
           EXP("^") {
               public double apply(double x, double y) {
                  return Math.pow(x, y);
               }
           },
           REMAINDER("%") {
               public double apply(double x, double y) {
                  return x % y;
               }
           };
           private final String symbol;
           ExtendedOperation(String symbol) {
              this.symbol = symbol;
           }
           @Override public String toString() {
              return symbol;
           }
       }
     虽然无法编写可扩展的枚举类型，却可以通过编写接口以及实现该接口的基础枚举类型，对它进行模拟。

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
```
