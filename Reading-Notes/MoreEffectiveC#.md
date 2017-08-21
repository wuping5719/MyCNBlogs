<h2>《More Effective C#》 :books: </h2> 

> [美] Bill Wagner 著    人民邮电出版社

```C#
1.使用 1.x 框架 API 的泛型版本。

2.恰当好处地定义约束。
 ① 搜索集合中第一个满足指定条件的对象。
 public static T FirstOrDefault<T> (this IEnumerable<T> sequence, Predicate<T> test)
 {
    foreach (T value in sequence)
       if (test(value))
           return value;
    return default(T);
 }

3.运行时检查泛型参数的类型并提供特定的算法。
 ① 设计一个将序列中的元素反序的类。
 private class ReverseStringEnumerator : IEnumerator<char> 
 {
    private string sourceSequence;
    private int currentIndex;
    public ReverseStringEnumerator (string source)
    {
       sourceSequence = source;
       currentIndex = source.Length;
    }
    #region IEnumerator<char> Members
    public char Current
    {
       get { return sourceSequence[currentIndex];  }
    }
    #endregion
    #region IDisposable Members
    public void Dispose() 
    {  
        // 无需实现 
    }
    #endregion
    #region IEnumerator Members
    object System.Collections.IEnumerator.Current 
    {
        get { return sourceSequence[currentIndex];  }
    }
    public bool MoveNext() 
    {
       return --currentIndex >= 0;
    }
    public void Reset() { currentIndex = sourceSequence.Length;  }
    #endregion
 }
 // ReverseEnumerable<T>.GetEnumerator() 需要检查参数的类型，并创建合适的枚举器类型。
 public IEnumerator<T> GetEnumerator()
 {
    // string 是个特例
    if (sourceSequence is string)
    {
       // 注意该转换，因为 T 在编译期可能不是 char
       return new ReverseStringEnumerator(sourceSequence as string) as IEnumerator<T>;
    }
    // 创建原有序列的副本，以便反序操作
    if (orginalSequence == null) 
    {
        if (sourceSequence is ICollection<T>)
        {
           ICollection<T> source = sourceSequence as ICollection<T>;
           orginalSequence = new List<T>(source.Count);
        }
        else
           orginalSequence = new List<T>();
        foreach (T item in sourceSequence)
           orginalSequence.Add(item);
    }
    return new ReverseEnumerator(orginalSequence);
 }

4.使用泛型强制编译期类型推断。

5.确保泛型类型支持可销毁对象。
 public void GetThingsDone()
 {
    T driver = new T();
    using(driver as IDiposable)
    {
       driver.DoWork();
    }
 }
 
6.使用委托定义类型参数上的方法约束。
  public static IEnumerable<TOutput> Merge<T1, T2, TOutput>(IEnumerable<T1> left, 
                                           IEnumerable<T2> right, Func<T1, T2, TOutput> generator)
  {
      IEnumerator<T1> leftSequence = left.GetEnumerator();
      IEnumerator<T2> rightSequence = right.GetEnumerator();
      while (leftSequence.MoveNext() && rightSequence.MoveNext())
      {
         yield return generator(leftSequence.Current, rightSequence.Current);
      }
  }
  
7.不要为基类或接口创建泛型的特殊实现。
 // 不是最好的解决方案，使用了运行时类型检查
 static void WriteMessage <T> (T obj)
 {
    if (obj is MyBase)
       WriteMessage(obj as MyBase);
    else if (obj is IMessageWriter)
       WriteMessage((IMessageWriter) obj);
    else
    {
       Console.Write("Inside WriteMessage<T>(T): ");
       Console.WriteLine(obj.ToString());
    }
 }
 
 8.尽可能使用泛型方法，除非需要将泛型参数用于实例的字段中。
   public static class Utils 
   {
       public static T Max<T> (T left, T right)
       {
          return Comparer<T>.Default.Compare(left, right) < 0 ? right : left;
       }
       public static double Max(double left, double right)
       {
          return Math.Max(left, right);
       }
       public static T Min<T> (T left, T right)
       {
          return return Comparer<T>.Default.Compare(left, right) < 0 ? left : right;
       }
       public static double Min(double left, double right)
       {
          return Math.Min(left, right);
       }
   }
 
 9.使用泛型元组替代 out 和 ref 参数。
   public struct Tuple<T1, T2> : IEquatable <Tuple<T1, T2>>
   {
      private readonly T1 first;
      public T1 first 
      {
         get { return first; }
      }
      private readonly T2 second;
      public T2 second 
      {
         get { return second; }
      }
      public Tuple(T1 f, T2 s)
      {
         first = f;
         second = s;
      }
   }
   
10.在实现泛型接口的同时也实现传统接口。
  public static bool operator <= (Name left, Name right)
  {
     if (left == null)
         return ture;
     return left.CompareTo(right) <= 0;
  }
  public static bool operator >= (Name left, Name right)
  {
     if (left == null)
         return right == null;
     return left.CompareTo(right) >= 0;
  }
  
11.使用线程池而不是创建线程。
  System.Threading.ThreadPool.QueueUserWorkItem (delegate (object X) 
  {
     for (int i = lowerBound; i < UpperBound; i++)
     {
        if (i % numThreads == thread)
        {
           double answer = Hero.FindRoot(i);
        }
     }
     // 减少计数器的值
     if (Interlocked.Decrement (ref workerThreads) == 0)
     {
        e.Set();   // 设置事件
     }
   });
   
12.使用 BackgroundWorker 实现线程间通信。
  BackgroundWorker backgroundWorkerExample = new BackgroundWorker();
  backgroundWorkerExample.DoWork += new DoWorkEventHandler(backgroundWorkerExample_DoWork);
  backgroundWorkerExample.RunWorkAsync();
  void backgroundWorkerExample_DoWork(object sender, DoWorkEventArgs e) { ... }
  
13.让 lock() 作为同步的第一选择。
  public sealed class LockHolder <T> : IDisposable where T class 
  {
     private T handle;
     private bool holdsLock;
     public LockHolder (T handle int milliSecondTimeout)
     {
        this.handle = handle;
        holdsLock = System.Threading.Monitor.TryEnter(handle, milliSecondTimeout);
     }
     public bool LockSuccessful 
     {
        get { return holdsLock; }
     }
     #region IDisposable Members 
     public void Dispose()
     {
        if (holdsLock)
           System.Threading.Monitor.Exit(handle);
        // 不要重复释放
        holdsLock = false;
     }
     #endregion
  }
  object lockHandle = new object();
  using (LockHolder<object> lockObj = new LockHolder<object>(lockHandle, 1000))
  {
     if (lockObj.lockSuccessful) { ... }
  }
  // 在此处析构
  
14.尽可能地减小锁对象的作用范围。
  private object syncHandle;
  private object GetSyncHandle() 
  {
     System.Threading.Interlocked.CompareExchange(ref syncHandle, new object(), null);
     return syncHandle;
  }
  public void AnotherMethod() 
  {
     lock (GetSyncHandle()) { ... }
  }
  
15.避免在锁定区域内调用外部代码。
  public void DoWork()
  {
      for(int count = 0; count < 100; count++)
      {
          lock (syncHandle)
          {
             System.Threading.Thread.Sleep(100);
             progressCounter++;
          }
          if (RaiseProgress != null)
             RaiseProgress(this, EventArgs.Empty);
      }
  }
  
16.理解 Windows 窗体和 WPF 中的跨线程调用。
  public static class ControlExtensions
  {
     public static void InvokeIfNeeded(this Control ctl, Action doit)
     {
        if (ctl.InvokeRequired)
           ctl.Invoke(doit);
        else
           doit();
     }
     public static void InvokeIfNeeded<T>(this Control ctl, Action<T> doit, T args)
     {
        if (ctl.InvokeRequired)
           ctl.Invoke(doit, args);
        else
           doit(args);
     }
  }
  
17.为序列创建可组合的 API。
  public static IEnumerable<T> UniqueV3<T>(IEnumerable<T> sequence)
  {
     Dictionary<T, T> uniqueVals = new Dictionary<T, T>();
     foreach (T item in sequence)
     {
        if (!uniqueVals.ContainsKey(item))
        {
           uniqueVals.Add(item, item);
           yield return item;
        }
     }
  }

18.将遍历和操作、谓词以及函数分开。
  public delegate T Transformer<T>(T element);
  public static IEnumerable<T> Transform<T>(IEnumerable<T> sequence, Transformer<T> method)
  {
     foreach(T element in sequence)
        yield return method(element);
  }
  // 使用匿名委托语法的 Transform
  foreach (int i in Transform(myInts, delegate(int Value)
  {
     return value * value;
  }));
  Console.WriteLine(i);
  // 使用 lambda 表达式语法的 Transform
  foreach (int i in Transform (myInts, (value) => value * value));
  Console.WriteLine(i);

19.根据需要生成序列中的元素。

20.使用函数参数降低耦合。
  public static IEnumerable<TResult> Join<T1, T2, TResult> (IEnumerable<T1> first,
                     IEnumerable<T2> second, Func<T1, T2, TResult> joinFunc)
  {
     using (IEnumerator<T1> firstSequence = first.GetEnumerator())
     {
         using (IEnumerator<T2> secondSequence = second.GetEnumerator())
         {
            while (firstSequence.MoveNext() && secondSequence.MoveNext())
            {
               yield return joinFunc(firstSequence.Current, secondSequence.Current);
            }
         }
     }
  }
  IEnumerable<string> result = Join(first, second, (one, two) => string.Format("{0} {1}", one, two));
  
21.让重载方法组尽可能清晰、最小化且完整。

22.定义方法后再重载操作符。

23.理解事件是如何增加对象间运行时耦合的。

24.仅声明非虚的事件。
  public abstract class WorkerEngineBase
  {
     public virtual event EventHandler <WorkerEventArgs> OnProgress;
     protected virtual WorkerEventArgs RaiseEvent(WorkerEventArgs args)
     {
        EventHandler<WorkerEventArgs> progHandler = OnProgress;
        if (progHandler != null)
        {
            progHandler(this, args);
        }
        return args;
     }
     public void DoLotsOfStuff()
     {
        for (int i = 0; i < 100; i++)
        {
            SomeWork();
            WorkerEventArgs args = new WorkerEventArgs();
            args.Percent = i;
            RaiseEvent(args);
            if (args.Cancel)
               return;
        } 
     }
     protected abstract void SomeWork();
  }
  public class WorkEngineDerived : WorkerEngineBase
  {
     protected override void SomeWork()
     {
         Thread.Sleep(50);
     }
     public override event EventHandler<WorkerEventArgs> OnProgress;
     protected override WorkerEventArgs RaiseEvent(WorkerEventArgs args)
     {
        EventHandler<WorkerEventArgs> progHandler = OnProgress;
        if (progHandler != null)
        {
            progHandler(this, args);
        }
        return args;
     }
  }

25.使用异常来报告方法的调用失败。
  public class DoesWorkThatMightFail
  {
      public bool TryDoWork()
      {
         if (!TestConditions())
            return false;
         Work();  // 仍有可能由于失败抛出异常，不过可能性很小
         return true;
      }
      public void DoWork()
      {
          Work(); // 在失败时抛出异常
      }
      private bool TestConditions()
      {
         // 实现省略
         return true;
      }
      private void Work()
      {
         // 执行实际操作
      }
  }

26.确保属性的行为与数据类似。

27.区分继承与组合。
  在你需要让类型重用其它类型的实现时，应该使用组合。若类型表示的是 Is A 关系，那么继承是更好的选择。

28.使用扩展方法增强现有接口。

29.使用扩展方法增强现有类型。
  public static IEnumerable<Customer> LostProspects(IEnumerable<Customer> targetList)
  {
     IEnumerable<Customer> answer = from c in targetList
                                    where DateTime.Now - c.LastOrderDate > TimeSpan.FormDays(30)
                                    select c;
     return answer;
  }

30.推荐使用隐式类型局部变量。
  public IEnumerable <string> FindCustomersStartingWith (string start)
  {
     var q = from c in db.Customers
             select c.ContactName;
     var q2 = q.Where (s => s.StartsWith(start));
     return q2;
  }
```
 
