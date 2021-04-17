## CAS(compare and swap):

### CAS概念

CAS机制是用于乐观锁中保证线程安全的机制

> 乐观锁：即无锁状态，假设在线程运行过程中是不存在资源争夺和冲突，不对操作资源加锁。但其使用CAS机制来保证线程安全性。与其对应的是悲观锁。

CAS机制需要三个值，在执行更新操作时，会比对目前 `var` 的值是否与期望值 `expect`相等，若相等，则继续；否则，不执行此操作：

- `Expect`: 更新前的期望值
- `new`: 更新后的结果值
- `var`: 操作的目标变量

意味着，当多个线程同时对一个资源执行写操作时，只有一个线程可以执行成功，其他线程因为出现与预期值与当前值不同，而放弃执行更新操作，以此达到线程安全。线程多与自旋结合实现CAS技术，更新操作失败的线程通过自旋重新进行更新操作，直至更新成功。**CAS机制本身是线程安全的，因为CAS是cpu层面的一个原子操作。**

### java中对CAS的实现：

java中实现CAS操作主要在 Unsafe类中，如：

```java
@HotSpotIntrinsicCandidate
public final native boolean compareAndSetInt(Object o, long offset,
                                             int expected,
                                             int x);
@HotSpotIntrinsicCandidate
public final native int compareAndExchangeInt(Object o, long offset,
                                              int expected,
                                              int x)
```

这些方法使用native关键字修饰，具体实现使用的是C++.

java使用Unsafe类，实现了一些原子类，如`AtomicInteger`, `AtomicDouble`等. 位于`java.util.concurrent.atomic.*`;

### CAS的问题

- ABA问题

  如果多个线程争夺资源的时候，thread1将A值更新B，thread2将B更新为A，当线程thread3的期望值为A，则thread3是能够成功执行的。

  `解决方案`: `AtomicStampedReference`给资源增加版本或时间戳。

- 自旋问题导致的资源浪费

  多个线程自旋进行CAS，导致CPU资源浪费。

  **解决方案**: 等待一段时长，再进行CAS

- 单个共享资源

  CAS仅能保证单个共享资源的原子操作，不能针对多个资源或多个操作。

  **解决方案**： 

  - jdk1.5之后`AtomicReference`
  - 使用锁

## AQS(AbstractQueuedSynchronizer):

AQS指抽象队列同步器，使用`abstract`关键字修饰。AQS是juc中的核心类，锁、CountDownLetch等类都是基于AQS实现的。

AQS的结构：<img src="https://p1.meituan.net/travelcube/82077ccf14127a87b77cefd1ccf562d3253591.png" alt="img"  />

类中定义一个嵌套类`Node`, 该类的每个实例表示等待队列中的一个节点。

![img](https://p1.meituan.net/travelcube/960271cf2b5c8a185eed23e98b72c75538637.png)

AQS中一个`State`字段，为同步状态，表示临界资源获得锁的情况。

- 在独占模式下：

  ![img](https://p0.meituan.net/travelcube/27605d483e8935da683a93be015713f331378.png)

- 在共享模式下：

  ![img](https://p0.meituan.net/travelcube/3f1e1a44f5b7d77000ba4f9476189b2e32806.png)

通过定义AQS中以下的方法，可以实现特别的多线程同步器：

```java
protected boolean isHeldExclusively()	//该线程是否正在独占资源。只有用到Condition才需要去实现它。
protected boolean tryAcquire(int arg)	//独占方式。arg为获取锁的次数，尝试获取资源，成功则返回True，失败则返回False。
protected boolean tryRelease(int arg)	//独占方式。arg为释放锁的次数，尝试释放资源，成功则返回True，失败则返回False。
protected int tryAcquireShared(int arg)	//共享方式。arg为获取锁的次数，尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
protected boolean tryReleaseShared(int arg) //共享方式。arg为释放锁的次数，尝试释放资源，如果释放后允许唤醒后续等待结点返回True，否则返回False。
```

如ReentrantLock的实现方式（非公平锁）：

![img](https://p1.meituan.net/travelcube/7aadb272069d871bdee8bf3a218eed8136919.png)

### 其他基于AQS实现的同步工具：

| 工具                   | 说明                                                         |
| ---------------------- | :----------------------------------------------------------- |
| ReentrantLock          | 使用AQS保存锁重复持有的次数。当一个线程获取锁时，ReentrantLock记录当前获得锁的线程标识，用于检测是否重复获取，以及错误线程试图解锁操作时异常情况的处理。 |
| Semaphore              | 使用AQS同步状态来保存信号量的当前计数。tryRelease会增加计数，acquireShared会减少计数。 |
| CountDownLatch         | 使用AQS同步状态来表示计数。计数为0时，所有的Acquire操作（CountDownLatch的await方法）才可以通过（自旋判断计数）。 |
| ReentrantReadWriteLock | 使用AQS同步状态中的16位保存写锁持有的次数，剩下的16位用于保存读锁的持有次数。 |
| ThreadPoolExecutor     | Worker利用AQS同步状态实现对独占线程变量的设置（tryAcquire和tryRelease）。 |

## 参考

- GitBook: [AQS](https://redspider.gitbook.io/concurrent/di-er-pian-yuan-li-pian/11#111-aqs-jian-jie)

- 美团技术文档：
  - [从ReentrantLock的实现看AQS的原理及应用](https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html)
  - [不可不说的Java“锁”事](https://tech.meituan.com/2018/11/15/java-lock.html)

