## Java容器的继承关系
![](https://i.loli.net/2021/04/19/brRxtg9XBWcADYu.gif)
## 几种容器
### List接口
#### ArrayList
使用`Object []`做为底层存储, 支持随机访问
#### LinkedList
使用双向链表做底层存储, 不支持随机访问. JDK1.6之前为循环链表, JDK1.7取消了循环结构.
### Set接口
#### HashSet
底层使用HashMap实现, 线程不安全
#### LinkedHashSet
继承HashSet, 使能够按照添加顺序遍历.
#### TreeSet
底层使用红黑树实现, 能够按照添加顺序进行遍历.
### Map接口
#### HashMap
JDK1.8的HashMap实现方式：散列表+位桶+链表+红黑树
HashMap的插入机制：
![](https://i.loli.net/2021/04/19/8PzsSnhkoGEDcr4.png)
HashMap的线程安全问题：
**JDK1.7:**JDK1.7中扩容的机制中，对于每个桶内的元素冲哈希后插入到新hashmap中使用的是头插法，在多线程并发情况下，多个线程的同时下的扩容，可能会造成桶内链表出现环，从而造成程序死循环。
**JDK1.8:**JDK1.8中put<K, V>的时候, 由于线程并发, 可能会出现覆盖的现象

#### HashTable
线程安全, 因为大部分方法使用了synchronized关键字修饰, 使用散列表 + 位桶 + 链表结构.
因为synchronized关键字的存在, 存储访问效率低.

