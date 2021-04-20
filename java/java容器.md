## Java容器的继承关系
![](https://i.loli.net/2021/04/19/brRxtg9XBWcADYu.gif)
## 几种容器
### HashMap
JDK1.8的HashMap实现方式：散列表+位桶+链表+红黑树
HashMap的插入机制：
![](https://i.loli.net/2021/04/19/8PzsSnhkoGEDcr4.png)
HashMap的线程安全问题：
**JDK1.7:**JDK1.7中扩容的机制中，对于每个桶内的元素冲哈希后插入到新hashmap中使用的是头插法，在多线程并发情况下，多个线程的同时下的扩容，可能会造成桶内链表出现环，从而造成程序死循环。
**JDK1.8:**JDK1.8中put<K, V>的时候, 由于线程并发, 可能会出现覆盖的现象


