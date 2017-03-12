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
```
