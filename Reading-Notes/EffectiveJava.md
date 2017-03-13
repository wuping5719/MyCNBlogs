<h2>《Effective Java》 :books: </h2> 
> 布洛克 (Bloch, Joshua) 著    机械工业出版社

```html
1.(考虑)用静态工厂方法代替构造器。
  例： public static Boolean valueOf(boolean b) {
         return b ? Boolean.TRUE : Boolean.FALSE;
       }
  (1) 静态工厂方法相对于公有构造器的优势：
    ① 静态工厂方法有名称;
    ② 静态工厂方法不必在每次调用它们的时候都创建一个新对象; (实例受控的类)
    ③ 静态工厂方法可以原返回类型的任何子类型对象;
    ④ 静态工厂方法在创建参数化类型实例的时候，它们使代码变得更加简洁。
  (2) 静态工厂方法的缺点：
    ① 类如果不含公有的或者受保护的构造器，就不能被子类化；
    ② 静态工厂方法与其他静态方法实际上没有任何区别。

2.遇到多个构造器参数时要考虑用构造器。(Bulider模式)

3.用私有构造器或者枚举类型强化 Singleton 属性。
  单元素的枚举类型已经成为实现 Singleton 的最佳方法。

4.通过私有构造器强化不可实例化的能力。
  public class UtilityClass {
     private UtilityClass() {
        throw new AssertionError();
     }
  }
  
5.避免创建不必要的对象。
  ① String s = "stringette";
  ② Class Person {
       private final Date birthDate;
       private static final Date BOOM_START;
       private static final Date BOOM_END;
       static {
          Calendar gmtCal = Calendar.getInstance(TimeZone.getTimeZone("GMT"));
          gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
          BOOM_START = gmtCal.getTime();
          gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
          BOOM_END = gmtCal.getTime();
       }
       public boolean isBabyBoomer() {
          return birthDate.compareTo(BOOM_START) >= 0 && birthDate.compareTo(BOOM_END) < 0;
       }
    }
  ③ 自动装箱。

6.消除过期的对象引用。
  ① 类自己管理内存，应该警惕内存泄漏问题;
  ② 内存泄露来源：缓存(在缓存之外存在对某个项的键的引用，用 WeakHashMap 代替缓存);
  ③ 内存泄露来源：监听器和其他回调。(只保存回调的弱引用)

7.避免使用终结方法。(finalizer)
  不应该依赖终结方法来更新重要的持久状态。
  使用终结方法有一个非常严重的(Severe)性能损失。
  显示的终止方法通常与 try-finally 结构结合起来使用，以确保及时终止。
    Foo foo = new Foo(...);
    try {
      ...
    } finally {
      foo.terminate();
    }
  终结方法与对象的本地对等体(native peer)有关。
    @Override protected void finalize() throws Throwable {
       try {
         ...
       } finally {
         super.finalize();
       }
    }
  把终结方法放在一个匿名的类中，该匿名类的唯一用途就是终结它的外用实例(enclosing instance)。该匿名类的单个实例被称为
终结方法守卫者(finalizer guardian)。
   public class Foo {
      private final Object finalizerGuardian = new Object() {
          @Override protected void finalize() throws Throwable {
             ...
          }
      };
   }
```
