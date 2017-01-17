<h2>《Java 并发编程实战》 :books: </h2> 
> Brian Goetz、 Tim Peierls、 Joshua Bloch、 Joseph Bowbeer、 David Holmes、 Doug Lea 著   

```html
 49.显式锁：
   与内置锁相比，显式的 Lock 提供了一些扩展功能，在处理锁的不可用性方面有着更高的灵活性，并且对队列行有着更好的控制。
但 ReentrantLock 不能完全替代 synchronized，只有在 synchronized 无法满足需求时，才应该使用它。
   读-写锁允许多个读线程并发地访问被保护的对象，当访问以读取操作为主的数据结构时，它能提高程序的可伸缩性。
```
