<h2>《Effective Java》 :books: </h2> 

> 布洛克 (Bloch, Joshua) 著    机械工业出版社

```java
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
    
23.请不要在新代码中使用原生态类型。
   (1) 如果使用原生态类型，就失掉了泛型在安全性和表述性方面的所有优势。
   (2) 如果使用像 List 这样的原生态类型，就会失掉类型安全性，但是如果使用像 List<Object> 这样的参数化类型，则不会。
   (3) 在类文字(class literal)中必须使用原生态类型。(List.class，String[].class)
   (4) 利用泛型来使用 instanceof 操作符的首选方法。
     if (o instanceof Set) {
        Set<?> m = (Set<?>) o;
        ...
     }

24.消除非受检警告。
   (1) 如果无法消除警告，同时可以证明引起警告的代码是类型安全的，才可以用一个 @SuppressWarning("unchecked") 注解
来禁止这条警告。
   (2) 应该始终在尽可能小的范围中使用 SuppressWarning 注解。
   (3) 每当使用 SuppressWarning("unchecked") 注解时，都要添加一条注释，说明为什么这么做是安全的。

25.列表优先于数组。
   (1) 数组是协变的(covariant)，泛型是不可变的(invariant)。
   (2) 数组是具体化的(reified)，泛型通过擦除(erasure)来实现的。

26.优先考虑泛型。

27.优先考虑泛型方法。
   (1) public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
           Set<E> result = new HashSet<E>(s1);
           result.addAll(s2);
           return result;
       }
   (2) public static <K, V> HashMap<K, V> newHashMap() {
           return new HashMap<K, V>();
       }
       Map<String, List<String>> anagrams = newHashMap();
   (3) public static <T extends Comparable<T>> T max(List<T> list) {
           Iterator<T> i = list.iterator();
           T result = i.next();
           while (i.hasNext()) {
               T t = i.next();
               if (t.compareTo(result) > 0)
                  result = t;
           }
           return result;
       }

28.利用有限通配符来提升 API 的灵活性。
   (1) public void pushAll(Iterable<? extends E> src) {
           for (E e : src)
               push(e);
       }
   (2) public void popAll(Collection<? super E> dst) {
           while (!isEmpty())
              dst.add(pop());
       }
     为了获得最大限度的灵活性，要在表示生产者或消费者的输入参数上使用通配符类型。
     PECS 表示 producer-extends，consumer-super。
     static <E> E reduce(List<? extends E> list, Function<E> f, E initVal);
     public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2);
     不要使用通配符类型作为返回类型。
   (3) public static <T extends Comparable <? super T>> T max(List<? extends T> list) {
           Iterator<? extends T> i = list.iterator();
           T result = i.next();
           while(i.hashNext()) {
              T t = i.next();
              if (t.compareTo(result) > 0)
                 result = t;
           }
           return result;
       }
    (4) public static void swap(List<?> list, int i, int j) {
            swapHelper(list, i, j);
        }
        private static <E> void swapHelper(List<E> list, int i, int j) {
            list.set(i, list.set(j, list.get(i)));
        }
      所有的 comparable 和 comparator 都是消费者。

29.优先考虑类型安全的异构器。
   (1) public class Favorites {
           private Map<Class<?>, Object> favorities = new HashMap<Class<?>, Object>();
           public <T> void putFavorite(Class<T> type, T instance) {
               if (type == null)
                   throw new NullPointerException("Type is null");
               favorities.put(type, instance);
           }
           public <T> getFavorite(Class<T> type) {
               return type.cast(favorities.get(type));
           }
       }
   (2) 在编译时读取类型未知的注解。
       static Annotation getAnnotation(AnnotatedElement element, String annotationTypeName) {
           Class<?> annotationType = null;
           try {
               annotationType = Class.forName(annotationTypeName);
           } catch (Exception ex) {
               throw new IllegalArgumentException(ex);
           }
           return element.getAnnotation(annotationType.asSubclass(Annotation.class));
       }

29.优先考虑类型安全的异构器。
   (1) public class Favorites {
           private Map<Class<?>, Object> favorities = new HashMap<Class<?>, Object>();
           public <T> void putFavorite(Class<T> type, T instance) {
               if (type == null)
                   throw new NullPointerException("Type is null");
               favorities.put(type, instance);
           }
           public <T> getFavorite(Class<T> type) {
               return type.cast(favorities.get(type));
           }
       }
   (2) 在编译时读取类型未知的注解。
       static Annotation getAnnotation(AnnotatedElement element, String annotationTypeName) {
           Class<?> annotationType = null;
           try {
               annotationType = Class.forName(annotationTypeName);
           } catch (Exception ex) {
               throw new IllegalArgumentException(ex);
           }
           return element.getAnnotation(annotationType.asSubclass(Annotation.class));
       }

30.用 enum 代替 int 常量。
   (1) public enum Apple { FUJI, PIPPIN, GRANNY_SMITH };
     为了将数据与枚举常量关联起来，得声明实例域，并编写一个带有数据并将数据保存在域中的构造器。
   (2) 特定常量的方法实现：
     public enum Operation {
        PLUS { double apply(double x, double y) { return x+y; } },
        MIUS { double apply(double x, double y) { return x-y; } },
        TIMES { double apply(double x, double y) { return x*y; } },
        DIVIDES { double apply(double x, double y) { return x/y; } };
        abstract double apply(double x, double y);
     }
    (3) 枚举中的 switch 语句适合于给外部的枚举类型增加特定于常量的行为。
```
