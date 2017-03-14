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
```
