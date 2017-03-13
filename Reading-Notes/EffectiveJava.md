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
   
  8.覆盖 equals 时请遵守通用约定。
  (1) 类的每个实例本质上都是唯一的;
  (2) 不关心类是否提供了"逻辑相等(logical equality)"的测试功能;
  (3) 超类已经覆盖了 equals, 从超类继承过来的行为对于子类也是合适的;
  (4) 类是私有的或是包级私有的，可以确定它的 equals 方法永远不会被调用。
     @Override public boolean equals(Object o) {
        throw new AssertionError();
     }
  equals 方法实现了等价关系：自反性、对称性、传递性和一致性。
  实现高质量 equals 方法的诀窍：
    ① 使用 == 操作符检查"参数是否为这个对象的引用";
    ② 使用 instanceof 操作符检查"参数是否为正确的类型";
    ③ 把参数转换成正确的类型;
    ④ 对于该类中的每个"关键(significant)"域，检查参数中的域是否与该对象中对应的域相匹配;
    ⑤ 编写完成 equals 方法之后，应该问自己三个问题：它是否是对称的、传递的、一致的?
   覆盖 equals 时总要覆盖 hashCode; 
   不要企图让 equals 方法过于智能;
   不要将 equals 声明中的 Object 对象替换为其他的类型。

9.覆盖 equals 时总要覆盖 hashCode。
  没有覆盖 hashCode 违反的关键约定是第二条：相等的对象必须具有相等的散列码。
  (1) 把某个非零的常数值，保存在一个名为 result 的 int 类型变量中。
  (2) 对于对象中每个关键域 f：
    Ⅰ.为该域计算 int 类型的散列码 c:
      ① boolean 类型，计算 (f ? 1 : 0);
      ② byte、char、short 或 int 类型，计算 (int) f;
      ③ long 类型，计算 (int)(f ^ (f >>> 32));
      ④ float 类型，计算 Float.floatToIntBits(f);
      ⑤ double 类型，计算 Double.doubleToLongBits(f)，按 ② 计算;
      ⑥ 对象引用，equals 递归调用 equals，则递归调用 hashCode;
      ⑦ 数组，每一个元素当作单独的域来处理。 Arrays.hashCode()
    Ⅱ.散列码 c 合并到 result 中：result = 31 * result + c;  (注：31 * result = (i << 5) - result)
  (3) 返回 result;
  (4) 写完 hashCode 方法之后，判断"相等的实例是否都具有相等的散列码"。
  不要试图从散列码计算中排除掉一个对象的关键部分来提高性能。

10.始终要覆盖 toString。
  toString 方法应该返回对象中包含的所有值得关注的信息。
  无论你是否决定指定格式，都应该在文档中明确地表明你的意图。
  都为 toString 返回值中包含的所有信息，提供一种编程式的访问途径。

11.谨慎地覆盖 clone。
  如果你覆盖了非 final 类中的 clone方法，则应该返回一个通用调用 super.clone 而得到的对象。
  对于实现 Cloneable 的类，我们总是期望它也提供一个功能适当的公有 clone 方法。
    @Override public PhoneNumber clone() {
       try {
          return (PhoneNumber) super.clone();
       } catch (CloneNotSupportedException e) {
          throw new AssertionError();
       }
    }
  协变返回类型作为泛型。
  永远不要让客户去做任何类库能够替客户完成的事情。
  clone 方法就是另一个构造器，你必须确保它不会伤害到原始的对象，并确保正确地创建被克隆对象中的约束条件。
    @Override public Stack clone() {
       try {
          Stack result = (Stack) super.clone();
          result.elements = elements.clone();
          return result;
       } catch (CloneNotSupportedException e) {
          throw new AssertionError();
       }
    }
  clone 架构与引用可变对象的 final 域的正常用法是不相兼容的。
  实现对象拷贝：提供一个拷贝构造器或拷贝工厂。
  拷贝工厂是类似拷贝构造器的静态工厂。
    public static Yum newInstance(Yum yum);
    
12.考虑实现 Comparable 接口。

13.使类和成员的可访问性最小化。
   (1) 尽可能地使每个类或者成员不被外界访问;
   私有的(private)：只有在声明该成员的顶层类内部才可以访问。
   包级私有的(package-private)(default)：声明该成员的包内部的任何类都可以访问。
   受保护的(protected)：声明该成员的类的子类可以访问。
   公有的(public)：在任何地方都可以访问。
   (2) 实例域决不能是公有的，包含公有可变域的类并不是线程安全的。
   (3) 类具有公有的静态 final 数组域，或者返回这种域的访问方法，这几乎总是错误的。
       public static final Thing[] VALUES = {...};  (安全漏洞)
     解决方法1:
       private static final Thing[] PRIVATE_VALUES = {...};
       public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES));
     解决方法2:
       private static final Thing[] PRIVATE_VALUES = {...};
       public static final Thing[] values() {
          return PRIVATE_VALUES.clone();
       } 

14.在公有类中使用访问方法而非公有域。

15.使可变性最小化。
  (1) 不要提供任何会修改对象状态的方法(mutator);
  (2) 保证类不会扩展;
  (3) 使所有的域都是 final 的;
  (4) 使所有的域都成为私有的;
  (5) 确保对于任何可变组件的互斥访问。

16.复合优先于继承。
  包装类(wrapper class)即装饰(Decorator)模式，不能用于回调框架(callback framework)。

17.要么为继承而设计，并提供文档说明，要么禁止继承。
  (1) 无论是 clone 还是 readObject，都不可以调用可覆盖的方法，不管是以直接还是间接的方式。
  (2) 对于那些并非为了安全地进行子类化而设计和编写文档的类，要禁止子类化。

18.接口优于抽象类。
  (1) 现有类可以很容易更新，以实现新的接口;
  (2) 接口是定义 mixin (混合类型)的理想选择;
  (3) 接口允许我们构造非层次结构的类型框架;
  通过对你导出的每个重要接口都提供一个抽象的骨架实现(skeletal implementation)类，把接口和抽象类的优点结合起来。

19.接口只用于定义类型。
  常量接口模式是对接口的不良使用。

20.类层次优于标签类。

21.用函数对象表示策略。(策略接口)
   public interface Comparator<T> {
       public int compare(T t1, T t2);
   }

22.优先考虑静态成员类。
   嵌套类(nested class)是指被定义在另一个类的内部的类。
   4种嵌套类：静态成员类(static member class)、非静态成员类(nostatic member class)、匿名类(anonymous class)
和局部类(local class)。
   如果声明成员类不要求访问外围实例，始终把 static 修饰符放在它的声明中，使它成为静态成员类。
   匿名类的常见用法：
    ① 动态地创建函数对象(function object);
    ② 创建过程对象(process object);
    ③ 在静态工厂方法的内部。
```
