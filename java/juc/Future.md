## Future<V>

### 概念

> A Future represents the result of an asynchronous computation. Methods are provided to check if the computation is complete, to wait for its completion, and to retrieve the result of the computation. The result can only be retrieved using method get when the computation has completed, blocking if necessary until it is ready.

ExecutorService 接口提供的线程提交执行的方法，返回Future的对象或者List：

```java
//接受Callable<T>接口线程
<T> Future<T> submit(Callable<T> task);
//接受Runnable接口线程
<T> Future<T> submit(Runnable task, T result);
<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
<T> T invokeAny(Collection<? extends Callable<T>> tasks)
```

在`AbstractExecutorService`中，对`submit`方法实现如下：

```java
public Future<?> submit(Runnable task) {
    if (task == null) throw new NullPointerException();
    RunnableFuture<Void> ftask = newTaskFor(task, null);
    execute(ftask);
    return ftask;
}
public <T> Future<T> submit(Runnable task, T result) {
    if (task == null) throw new NullPointerException();
    RunnableFuture<T> ftask = newTaskFor(task, result);
    execute(ftask);
    return ftask;
}
```

`newTaskFor`方法为创建一个FutureTask对象：`new FutureTask<T>(callable);` FutureTask类实现Future接口的实现，同时实现了RunnableFuture接口，而RunnableFuture接口则继承了Runnable接口。在FutureTask类中，如果接收的线程是继承`Runnable`接口的，则会调用`Executors.callable(Runnable task)`方法转为`Callable`接口线程。

FutureTask实现关系：

![](https://i.loli.net/2021/04/12/adgiO6CqI1h2BeN.png)