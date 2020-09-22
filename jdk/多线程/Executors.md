# <div style="text-align:center;color:#FF9900">Executors</div>

## 1. 关键API

### 1.1 生成固定大小的线程池

```Java
// 生成固定大小的线程池
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue<Runnable>());
}

// corePoolSize —— 最小线程数；如果allowCoreThreadTimeOut设置为true，线程池最后会减少到0；
public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue) {
          this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
               Executors.defaultThreadFactory(), defaultHandler);
}
```
对于`allowCoreThreadTimeOut`
> If false (default), `core threads` stay alive even when idle.
> If true, `core threads` use keepAliveTime to time out waiting for work.

* **corePoolSize：**

    好好理解下面一段话：

    > 线程池的基本大小，即在没有任务需要执行的时候线程池的大小，并且只有在`工作队列满了`的情况下**才会创建超出这个数量的线程**。

    而**工作队列**指的是上面代码中的`new LinkedBlockingQueue<Runnable>()`，它的默认容量是`Integer.MAX_VALUE`， 所以不可能（或很难）放满。你可以手动设置队列（BlockingQueue）的容量（比如：8），但是，仅仅这样就可以了吗？不！这个BlockingQueue和maximumPoolSize大有关系：

    * **BlockingQueue + maximumPoolSize >= 最高突发任务数**

      当所需线程数大于BlockingQueue容量的时候，多出的无法进入队列的任务就会创建新线程来执行，如果maximumPoolSize设置为有限值（如：maximumPoolSize=8），那么，当无法进入队列的任务数超过8（maximumPoolSize）时，就会抛出异常，所以，始终要保证: *BlockingQueue + maximumPoolSize >= 最高突发任务数*
      **个人理解**：如果曾经corePoolSize执行过task，再添加N个task（假设N大于queue的长度，假设queue长度为Q），那么线程池会先填满queue（即添加Q个task到queue中），然后创建N-Q个线程去执行没有添加到queue中的task，假设remaining-thread（剩余可创建线程数，假设RT，corePoolSize ≤ RT ≤ maximumPoolSize）小于N-Q（即剩余的N-Q个任务有线程可执行），那么线程池就会报错RejectedExecutionException，线程池不会利用已经存在的线程（即使你认为这些线程中的task已经执行完成）
      > **补充**：当添加的task数量大于Q之后，需要注意`BlockingQueue`的特性(`BlockingQueue.put`执行后，被`BlockingQueue.take`阻塞的`线程`不能够立马获取到element<队列元素>，element从放入队列到被取出队列，中间有时间间隔，一般间隔长度是jvm命令执行时间的量级`ns纳秒`， 上面的问题就在于此)，最好保证队列的`head`元素与即将插入的元素之间`有一定的时间间隔`，比如：
      > ```java
      >   fixedThreadPool.submit(newRunnable());
      >   // 添加一个时间间隔，保证之前的element被及时取走，留下空位给即将插入的element
      >   Thread.sleep(1);
      >   fixedThreadPool.submit(newRunnable());
      >```



### 1.2 CachedThreadPool

newCachedThreadPool()`生成一个初始大小为0，空闲时间为60s，没有上限的连接池：

```java
public static ExecutorService newCachedThreadPool() {
	return new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

`newCachedThreadPool(ThreadFactory threadFactory)`同上；

* 关于`SynchronousQueue`可以参考[博客](https://blog.csdn.net/yanyan19880509/article/details/52562039)

### 1.3 ScheduledThreadPool

`newScheduledThreadPool(int corePoolSize)`和`newScheduledThreadPool(int corePoolSize, ThreadFactory threadFactory)`生成延迟或定期执行任务的线程池；


  * ScheduledExecutorService#`scheduleWithFixedDelay`：是`前一次执行结束`到`后一次执行开始`的间隔为`delay`；

  * ScheduledExecutorService#`scheduleAtFixedRate`：执行时刻是`initialDelay`、`initialDelay + period`、`initialDelay + period * 2`、`initialDelay + period * 3` 。。。等，如果任务执行时间大于`period`，那么前一次执行完后立马执行下一个；
### 1.4 单线程

  - `newSingleThreadExecutor()`生成一个单线程并执行任务队列；

  - 如果单线程因为执行任务而意外中断或关闭，那么会生成一个新的线程代替旧的线程继续执行接下来的任务；

  - `newSingleThreadExecutor()`和`newFixedThreadPool(1)`区别：

    ```java
     // final ExecutorService single = Executors.newSingleThreadExecutor();
        final ExecutorService fixed = Executors.newFixedThreadPool(1);
        ThreadPoolExecutor executor = (ThreadPoolExecutor) fixed;
        executor.setCorePoolSize(4);  // newFixedThreadPool(1)可以再设置大小
    ```
* **单线程 + 定时/延迟**
  `newSingleThreadScheduledExecutor`
### 1.5 newWorkStealingPool()

  作用：貌似是尽可能地利用所有处理器，生成一个线程池；[参考](https://cloud.tencent.com/developer/article/1362826)

#### 1.5.1 newWorkStealingPool(int parallelism)
作用：最大线程数为`parallelism`，即使`parallelism`大于/小于处理器核心数量；

## 2. 原理与概念

* 在刚刚创建ThreadPoolExecutor的时候，线程并不会立即启动，而是要等到有任务提交时才会启动，除非调用了`prestartCoreThread-启动一个coreThread`/`prestartAllCoreThreads-启动所有coreThread`事先启动核心线程

* `largestPoolSize` - 该变量记录了线程池在整个生命周期中`曾经`出现的最大线程个数


## 3. 遗留问题
* corePoolSize是如何维持的？
  **已解决**：`Worker.runWorker`中的`getTask`（Jdk8-ThreadPoolExecutor-1127行）方法中`BlockingQueue`进行阻塞，以达到保持corePool线程的目的；


## 4. 链接
[多线程异常处理] | [AIO详解] | [Java并发控制：ReentrantLock Condition使用详解] | [ThreadLocal]


[多线程异常处理]:https://juejin.im/post/5d27c3e6518825451f65ee15
[AIO详解]:https://segmentfault.com/a/1190000020364149
[Java并发控制：ReentrantLock Condition使用详解]:https://www.cnblogs.com/hongdada/p/6150699.html
[ThreadLocal]:https://developer.aliyun.com/article/772194
