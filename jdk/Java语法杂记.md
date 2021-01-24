# <div style="text-align:center;color:#FF9900">Java语法杂记</div>
* `printf`函数中`%n`和`\n`都表示换行；

* 关于断言：
  - 断言的形式：
    - `asser condition[:expr]`,例：`assert(name!=null):"变量name为空null"`;
  - `-ea`选项使断言有效，也可以使用`-da`选项使断言无效（默认为无效）；
  - 可以通过在-ea或-da后面指定包名来使一个包的断言有效或无效
    `-da:com.test`
  - 要使一个包中的所有子包中的断言能够有效或无效，在包名后加上三个点
    `-da:com.test...`

* `spring`中的`Assert`类并不是`java`断言机制的使用，而是一种检查参数有效性的便捷手段，运行时是有效的；

* 踩坑：注意`ArrayList`中的`remove(int)`和`remove(Object)`的区别；在`ArrayList<Interger>`情况下，小心出错；

* **strictfp**关键字

  *`strictfp`关键字确保您将在每个平台上获得相同的结果，如果在浮点变量中执行操作。 不同平台的精度可能不同，这就是为什么java编程语言提供了`strictfp`关键字，它用于在每个平台上获得相同的结果。*

  * `strictfp`关键字可以应用于方法，类和接口

    ```java
    strictfp class A{}//strictfp applied on class

    strictfp interface M{}//strictfp applied on interface

    class B{
        strictfp void m(){}//strictfp applied on method
    }
    ```
#### try-with-resources
```Java
try (
java.util.zip.ZipFile zf = new java.util.zip.ZipFile(zipFileName);
java.io.BufferedWriter writer = java.nio.file.Files.newBufferedWriter(outputFilePath, charset)
) {
    // try块
} catch(XxxException e){

}
```
无论`try`语句块结果如何，最后resources都会关闭，关闭的顺序和创建的顺序相反；
> * 一个 try-with-resources 语句可以像普通的 try 语句那样有 catch 和 finally 块。在try-with-resources 语句中, 任意的 catch 或者 finally 块都是在声明的资源被关闭以后才运行。
> * 与try...catch...相反, 在 try-with-resources中, 如果 try 块和 try-with-resources 语句均抛出异常, 那么  try-with-resources 将抛出从 try 块中抛出的异常;  try-with-resources 块抛出的异常被抑制了;可以使用Throwable.getSuppressed 方法检索被抑制的异常信息
> * 实现了AutoCloseable 或 Closeable 接口的类均可以使用try-with-resources 语句;


## 1. 自动类型转换

### 1.1 基本类型

##### 示例一

**byte + byte = int**

> [JLS 5.6.2](http://java.sun.com/docs/books/jls/third_edition/html/conversions.html#5.6.2) makes it clear：
>
> When an operator applies binary numeric promotion to a pair of operands, each of which must denote a value that is convertible to a numeric type, the following rules apply, in order, using widening conversion (§5.1.2) to convert operands as necessary:
>
> * If any of the operands is of a reference type, unboxing conversion (§5.1.8) is performed. Then:
> * If either operand is of type double, the other is converted to double.
> * Otherwise, if either operand is of type float, the other is converted to float.
> * Otherwise, if either operand is of type long, the other is converted to long.
> * **Otherwise, both operands are converted to type int.**

> It means that Java prefers to treat smaller data types as ints, since any modern processor has at least 32-bit words anyway.



## 2. 泛型

### 2.1 泛型类

**泛型类，是在实例化类的时候指明泛型的具体类型；**



### 2.2 泛型方法

**泛型方法，是在调用方法的时候指明泛型的具体类型；**

```java
/**
 * 泛型方法的基本介绍
 * @param tClass 传入的泛型实参
 * @return T 返回值为T类型
 * 说明：
 *     1）public 与 返回值中间<T>非常重要，可以理解为声明此方法为泛型方法。
 *     2）只有声明了<T>的方法才是泛型方法，泛型类中的使用了泛型的成员方法并不是泛型方法。
 *     3）<T>表明该方法将使用泛型类型T，此时才可以在方法中使用泛型类型T。
 *     4）与泛型类的定义一样，此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型。
 */
public <T> T genericMethod(Class<T> tClass)throws InstantiationException ,
  IllegalAccessException{
        T instance = tClass.newInstance();
        return instance;
}
```

**注**：没有声明\<T>的方法不是泛型方法；


***
1. `Queue`的`add/remove/element`和`offer/poll/peek`有点区别（当操作到达边界时是否抛出异常），但是不同的实现类又做了不同的处理，`PriorityQueue`；
2. 一般`Queue`是不能插入`null`元素的，因为`null`是判断队列是否为empty的依据，但是也有例外，如：`LinkedList`（它的元素进行了封装，`java.util.LinkedList.Node`）；
3. `BlockingQueue`是`Queue`的子接口，多出了`put/take`方法，会阻塞；
4. 清空`BlockingQueue`的方法`drainTo()`；
5. 对象头大小是2个字长或3个字长（数组，需要一个字长保存数据大小）；
6. 锁升级：无 ——> 自旋  ——>  偏向锁 ——> 轻量级 ——> 重量级（自旋是发生在`偏向锁`的过程中【（[参考][深入分析Synchronized原理]）】）
7. 自旋锁解决的问题：线程的阻塞和唤醒需要CPU从用户态转为核心态（[参考][深入分析Synchronized原理]）
8. 自旋锁适用于锁保护的临界区很小的情况，临界区很小的话，锁占用的时间就很短（[参考][深入分析Synchronized原理]）
9. 自旋等待不能替代阻塞，虽然它可以避免线程切换带来的开销，但是它占用了CPU处理器的时间。（[参考][深入分析Synchronized原理]）
10. 如果并发数较大同时同步代码块执行时间较长，则被多个线程同时访问的概率就很大，就可以使用参数-XX:-UseBiasedLocking来禁止偏向锁（[参考][深入分析Synchronized原理]）
11. 轻量级锁的加锁解锁操作是需要依赖多次CAS原子指令的，而偏向锁只需要在置换ThreadID的时候依赖一次CAS原子指令；轻量级锁是为了在线程交替执行同步块时提高性能，而偏向锁则是在只有一个线程执行同步块时进一步提高性能。（[参考][深入分析Synchronized原理]）
12. 本质上偏向锁就是为了消除CAS，降低Cache一致性流量（[参考][深入分析Synchronized原理]）
13. 对于轻量级锁，其性能提升的依据是 “对于绝大部分的锁，在整个生命周期内都是不会存在竞争的”（[参考][深入分析Synchronized原理]）
14. 轻量级锁所适应的场景是线程交替执行同步块的情况，如果存在同一时间访问同一锁的情况，必然就会导致轻量级锁膨胀为重量级锁。（[参考][深入分析Synchronized原理]）
15. 重量级锁的`重`在于重量级锁会使用到`底层操作系统级互斥锁Mutex`，会导致`用户态/内核态`之间的切换，这是个性能消耗大户；
16. 锁升级过程[图](https://upload-images.jianshu.io/upload_images/2062729-61dfb07d48d8588c.png)
17. 代码实现线程阻塞的方法：`LockSupport.park(Object)`；
18. CLH锁也是一种基于链表的可扩展、高性能、公平的自旋锁，申请线程只在本地变量上自旋，它不断轮询前驱的状态，如果发现前驱释放了锁就结束自旋（[参考](https://coderbee.net/index.php/concurrent/20131115/577)），`AQS`也是基于CLH队列锁，只不过内部维护的队列是一个双向链表，链表节点是一个Node对象（[参考](https://zhuanlan.zhihu.com/p/50984817)）
19. `HashTable`和`ConcurrentHashMap`的key和value都不能为null；
20. `ConcurrentSkipListMap`的增序遍历（iterate）比逆序性能更好；（参见javadoc）
21. 多线程协调，可使用`CountDownLatch`非常有用；还有`CyclicBarrier`（await方法是借助Condition实现的）；
22. `ParallelScavenge`收集器会自动调整S0和S1的大小，所以，此收集器下，S0和S1大小不一定相等，如果想避免应用动态调整，可以参考`-XX:-UseAdaptiveSizePolicy`参数；
23. 动态年龄判断机制，可以借助`-XX:+PrintTenuringDistribution`参数进行观察；
24. 查看JVM实际参数，请[参考](https://www.cnblogs.com/cellei/p/12164708.html)
25. CPU负载高时，代码位置定位步骤：
    A. top命令找出负载高的`进程`；
    B. `top -Hp 进程id`，找出进程里负载高的`线程`
    C. 使用jstack打印出对应线程的栈信息；
26. SpringBoot的配置文件优先级（[参考](https://blog.csdn.net/J080624/article/details/80508606)）
27. SpringCloud开启自我保护时，假设A服务的部分节点由于网络异常触发自我保护，那么消费者还会调用异常的服务吗？还是只调用正常的服务？【<font color="red">待解决</font>】



[深入分析Synchronized原理]:https://www.cnblogs.com/aspirant/p/11470858.html
