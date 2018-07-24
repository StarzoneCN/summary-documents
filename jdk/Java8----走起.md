# JAVA8

### 行为参数化

* **@FunctionalInterface** 
  * 此注解会检查接口是不是函数式接口，如果不是，则会报错：”*Multiple non-overriding abstract methods found in interface Fo*o” 
* **倒序**
  * list.sort(comparing(Apple::getWeight).`reversed`()); 
* **比较器链**
  * list.sort(comparing(Apple::getWeight) .`thenComparing`(Apple::getCountry));  
* **谓语复合**
  * `Predicate`接口的`negate`、`and`和`or`方法
    * 不是红苹果，eg: Predicate notRedApple = redApple.`negate`() 
* **函数复合**
  * `andThen`和`compose`，[参考][composeAndThen]

    ```java
    Function<Integer, Integer> times2 = e -> e * 2;
    Function<Integer, Integer> squared = e -> e * e;
    
    times2.compose(squared).apply(4);  
    // Returns 32
    times2.andThen(squared).apply(4);  
    // Returns 64
    ```

    

### 方法引用

* `方法引用`就是`Lambda`表达式的快捷写法

  * eg：(Apple a) -> a.getWeight()  等效  Apple::getWeight() 

* 方法引用有三类

  * `静态方法` ：`Integer`的`parseInt`方法，写作`Integer::parseInt` 

  * `参数类型的实例方法`：`(Apple a) -> a.getWeight()` 可以换成`Apple::getWeight` 

  * `Lambda外的对象的实例方法`：

    ```java
    package com.example.demo.multi.springBoot;
    
    import com.example.demo.multi.springBoot.entity.User;
    import org.junit.Test;
    import java.util.function.Consumer;
    
    public class EasyTester {
    
        @Test
        public void test() {
            EasyTester easyTester = new EasyTester();  // Lambda外的对象easyTester
            apply(easyTester::printUserName);  // 方法引用，引用easyTester对象的printUserName方法
        }
    
        public void apply(Consumer<? super User> function){
            User user = new User();
            user.setName("Tom");
            user.setAge(10);
            function.accept(user);
        }
    
        public void printUserName(User user) {
            System.out.println("The user's name is " + user.getName());
        }
    }
    ```

* 其他方法引用类型

  * 构造方法引用：`ClassName::new` ，与指向静态方法的引用类似 



### Stream

* `Guava`、`Apache`和`lambdaj`等库也都实现了类似的流功能

* 流的源可以是`集合`、`数组`或`输入/输出资源` ，从有序集合生成流时会保留原有的顺序

* 流的操作方法：`filter`、`map`、`reduce`、`find`、`match`、`sort` 、`flatMap`

* 关于`flatMap`

  ```java
  List<String> uniqueCharacters = words.stream()
   	.map(w -> w.split(""))
   	.flatMap(Arrays::stream)
   	.distinct()
   	.collect(Collectors.toList());
  ```

  `flatMap`类似于降维；

* `集合`讲的是数据，`流`讲的是计算 
  * *流和集合的区别*就好比`DVD`和`流媒体`的区别：集合着急着创建数据，然后在处理；流则是边创建边使用；

* 流只能被消费一次：

  ```java
  Stream<String> s = list.stream();
  s.forEach(System.out::println);
  s.forEach(System.out::println);  // 这里会报错
  ```

* 流是`内部迭代`，而集合是`外部迭代`；

* 流的操作类型

  * 中间操作：filter、map、limit、reduce、match、sort、distinct、skip、flatMap（流的扁平化）等
  * 终端操作：collect、forEach、count、reduce

* 流的查找与匹配

  * allMatch、anyMatch、noneMatch、findFirst、findAny 

* `limit`操作与`短路`技巧、`匹配`操作与`短路`

  > 有些操作不需要处理整个流就能得到结果。例如，假设你需要对一个用and连起来的大布 尔表达式求值。不管表达式有多长，你只需找到一个表达式为false，就可以推断整个表达式 将返回false，所以用不着计算整个表达式。这就是短路。 
  >
  > 对于流而言，某些操作（例如allMatch、anyMatch、noneMatch、findFirst和findAny） 不用处理整个流就能得到结果。只要找到一个元素，就可以有结果了。同样，limit也是一个 短路操作：它只需要创建一个给定大小的流，而用不着处理流中所有的元素。在碰到无限大小 的流的时候，这种操作就有用了：它们可以把无限流变成有限流。 

* `《Java8实战》`*P105*页 - 一个优秀的求勾股数的实例，以及优化实例；

* 

#### Optional类

- Optional类（java.util.Optional）是一个容器类，代表一个值存在或不存在。 
- 关键API
  - isPresent()
  - ifPresent(Consumer block)
  - T get()  - 值存在时返回值，否则抛出一个NoSuchElement异常 
  - T orElse(T other)  - 值存在时返回值，否则返回一个默认值 

#### 归约

* reduce 

  ```java
  int sum = numbers.stream().reduce(0, Integer::sum); 
  // 无初始值
  Optional<Integer> sum = numbers.stream().reduce((a, b) -> (a + b));  // 因为，如果没有初始值，并且数组为空的话，不知道返回什么，所以，这里返回Optional类
  ```
  [扩展][reduce]

* **归约方法的优势与并行化**、

  ```java
  // 如果使用For-each实现并行，那简直就是折磨
  // 但要并行执行这段代码也要满足一定的条件：传递给reduce的Lambda不能更改状态（如实例变量），而且操作必须满足结合律才可以按任意顺序执行。
  int sum = numbers.parallelStream().reduce(0, Integer::sum);
  ```

#### 流的状态

* `sort`或`distinct`等操作，从流中排序和删除重复项时都需要知道*先前的历史* ，我们把这些操作叫作**有状态操作**；

#### 有界和无界

* 无界
  * 排序要求所有元素都放入缓冲区后才能给输出流加入一个项目，这一操作的存储要求是无界的；
* 有界
  * 如reduce、sum、max等操作需要内部状态来累积结果。在我们的例子里就是一个int或double，而int只需要4个字节，double只需要8个字节；

#### 流操作总结

|   操作    |        类型         |  返回类型   |
| :-------: | :-----------------: | :---------: |
|  filter   |        中间         | Stream\<T\> |
| distinct  | 中间  (有状态-无界) | Stream\<T\> |
|   skip    | 中间 (有状态-有界)  | Stream\<T\> |
|   limit   | 中间 (有状态-有界)  | Stream\<T\> |
|    map    |        中间         | Stream\<T\> |
|  flatMap  |        中间         | Stream\<T\> |
|  sorted   | 中间  (有状态-无界) | Stream\<T\> |
| anyMatch  |        终端         |   boolean   |
| noneMatch |        终端         |   boolean   |
| allMatch  |        终端         |   boolean   |
|  findAny  |        终端         |  Optional   |
| findFirst |        终端         |  Optional   |
|  forEach  |        终端         |    void     |
|  collect  |        终端         |      R      |
|  reduce   | 终端 (有状态-有界)  |  Optional   |
|   count   |        终端         |    long     |

#### 流中基本类型装箱问题

* `IntStream`、`DoubleStream`和 `LongStream` 

  > IntStream、DoubleStream和 LongStream，分别将流中的元素特化为int、long和double，从而避免了暗含的装箱成本。每 个接口都带来了进行常用数值归约的新方法，比如对数值流求和的sum，找到最大元素的max。 此外还有在必要时再把它们转换回对象流的方法 。

* 将流转换为特化版本的常用方法是mapToInt、mapToDouble和mapToLong

* 特化流转非特化流 - intStream.boxed()

* **特化流API**

  * IntStream.range(1, 100)
  * IntStream.rangeClosed(1, 100) 

#### 流的创建

* Stream.of()

  ```java
  Stream<String> stream = Stream.of("Java 8 ", "Lambdas ", "In ", "Action"); 
  ```

* 创建空流

  ```java
  Stream<String> emptyStream = Stream.empty();
  ```

* 数组创建流

  ```java
  Arrays.stream(numbers)
  ```

* 由文件创建流

  ```java
  // Files.lines得到一个流，其中的每个元素都是给定文件中的一行
  Stream<String> lines = Files.lines(Paths.get("data.txt"), Charset.defaultCharset())
  ```

* `Stream.iterate`和`Stream.generate` 

  > Stream API提供了两个静态方法来从函数生成流：Stream.iterate和Stream.generate。 这两个操作可以创建所谓的无限流：不像从固定集合创建的流那样有固定大小的流。由iterate 和generate产生的流会用给定的函数按需创建值，因此可以无穷无尽地计算下去！一般来说， 应该使用limit(n)来对这种流加以限制，以避免打印无穷多个值。 

  ```java
  // iterate
  Stream.iterate(0, n -> n + 2)
   	.limit(10)
   	.forEach(System.out::println); 
  // generate
  Stream.generate(Math::random)
   	.limit(5)
  	.forEach(System.out::println); 
  ```

  * 《Java8实战》P109 - 副作用的例子    ——  *匿名类和Lambda的区别* 

    > 相比之下，使用iterate的方法则是纯粹不变的：它没有修改现有状态， 但在每次迭代时会创建新的元组。你将在第7章了解到，你应该始终采用不变的方法 

#### 终端方法

* `collect`

  ```java
  // 分组
  Map<Currency, List<Transaction>> transactionsByCurrencies = transactions.stream().collect(groupingBy(Transaction::getCurrency));
  ```

* `groupingBy`

  * 一级分组

    ```java
    menu.stream().collect(groupingBy(Dish::getType));
    ```

  * 二级分组

    ```java
    // 结果是个两级Map
    menu.stream().collect(groupingBy(Dish::getType,
     	groupingBy(dish -> {
     		if (dish.getCalories() <= 400) 
     			return CaloricLevel.DIET;
     		else if (dish.getCalories() <= 700) 
     			return CaloricLevel.NORMAL; 
     		else 
     			return CaloricLevel.FAT;
     	}))); 
    ```

    > Collectors.groupingBy工厂方法创 建的收集器，它除了普通的分类函数之外，还可以接受collector类型的第二个参数；所以，双参数的groupingBy的第二个参数不一定要是groupingBy，也可以是Collectors.counting收集器，而结果就是 *{MEAT=3, FISH=2, OTHER=4}*  

    * 普通的单参数groupingBy(f)（其中f是分类函数）实际上是groupingBy(f, toList())的简便写法。 

* `partitioningBy` 是`groupingBy`的特殊情况

* `Collectors.characteristics`方法决定是否可以使用并行方式处理流 - *《Java8实战》P133*

* 想策底明白终端操作，就必须明白`Collector` 接口中的5个方法；





### 其他

* **Spliterator**

* *Jdk8*中时间相关类`LocalDateTime`和`DateTimeFormatter`；

  * `Joda-Time` — 优秀的时间lib；[参考][joda-time]

* **synchronized 降低性能的原因**

  - 多核CPU的每个处理器内核都有独立的高速缓存。加锁需要这些高速缓存同步运行，然而这又需要在内核间进行较慢的缓存一致性协议通信

* **变量**

  * 思考下面例子的输出结果

    ```java
    public class MeaningOfThis {
    
        public final int value = 4;   // value
    
        public void doIt() {
            int value = 6;   // value
            Runnable r = new Runnable() {
                public final int value = 5;   // value
    
                public void run() {
                    int value = 10;   // value
                    System.out.println(this.value);
                }
            };
            r.run();
        }
    
        public static void main(String... args) {
            MeaningOfThis m = new MeaningOfThis();
            m.doIt();
        }
    }
    ```

  * `实例变量`都存储在`堆`中，而`局部变量`则保存在`栈`上 

  * 下面的代码会报错

    ```java
    int portNumber = 1337;
    Runnable r = () -> System.out.println(portNumber);  // Variable used in lambda expression should be final or effectively final
    portNumber = 31337; 
    ```

    * 第一，实例变量和局部变量背后的实现有一 个关键不同。*实例变量都存储在堆中，而局部变量则保存在栈上*。如果Lambda可以直接访问局部变量，而且Lambda是在一个线程中使用的，则使用Lambda的线程，可能会在分配该变量的线 程将这个变量收回之后，去访问该变量。因此，Java在访问自由局部变量时，实际上是在访问它 的副本，而不是访问原始变量。如果局部变量仅仅赋值一次那就没有什么区别了——因此就有了 这个限制。
    * 第二，这一限制不鼓励你使用改变外部变量的典型命令式编程模式  

### Questions

* 









[composeAndThen]:http://www.importnew.com/17209.html
[joda-time]:https://www.jianshu.com/p/efdeda608780
[reduce]:https://blog.csdn.net/IO_Field/article/details/54971679