https://stackoverflow.com/questions/6559308/how-does-lmaxs-disruptor-pattern-work

http://java-is-the-new-c.blogspot.com/2014/01/comparision-of-different-concurrency.html

http://nikgrozev.com/2015/07/14/overview-of-modern-concurrency-and-parallelism-concepts/

https://stackoverflow.com/questions/27794459/how-to-implement-custom-actor-mailbox-using-disruptor

https://coolshell.cn/articles/9169.html

# Java Thread
java的并发编程本质上可以总结为三种场景：
1. 多线程共享变量，通过锁机制控制共享变量的并发访问，容易产生死锁
2. 生产者与消费者模式，通过队列实现消息交换
3. 某个线程等待其他线程全部执行完再执行的情形：CyclicBarrier， （本质是mutex互斥变量）

# Scala Actor
http://blog.csdn.net/jmppok/article/details/17264495
