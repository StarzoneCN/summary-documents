# <div style="text-align:center;color:#FF9900">JVM参数调优</div>
### <span style="color:green;">区域大小<span>
1. `-Xmn`：设置新生代大小，将NewSize与MaxNewSize设为一致，如`-Xmn256m`同下：
    ```
    -XX:NewSize=256m
    -XX:MaxNewSize=256m
    ```
    **建议**：如果将新生代的值设置过大，那么一次新生代GC的时间就会过长；如果将新生代的值设置过小，又会引起频繁新生代GC。官方建议该值设置在堆最大值的25%-50%区间内。

2. `-Xss`：设置虚拟机线程栈大小，linux默认为`1m`，windows的默认值为`0`（实际上，虚拟机会根据虚拟内存计算出默认值）；效果同`-XX:ThreadStackSize=size`；一般几个系统的默认值如下：
  > Linux/ARM (32-bit): 320 KB
  Linux/i386 (32-bit): 320 KB
  Linux/x64 (64-bit): 1024 KB
  OS X (64-bit): 1024 KB
  Oracle Solaris/i386 (32-bit): 320 KB
  Oracle Solaris/x64 (64-bit): 1024 KB

3. 设置**永久代**：`-XX:MaxMetaspaceSize=size`（jdk8之前：`-XX:MaxPermSize=size`）；

4. 错误转储：`-XX:+HeapDumpOnOutOfMemoryError`，还可使用选项`-XX:HeapDumpPath=path`指定文件地址，示例如下(%p表示进程PID)：
    ```
     -XX:HeapDumpPath=/data/log/java_pid%p.hprof
    ```

5. 内存溢出后执行命令：`-XX:OnOutOfMemoryError=string`;
  > 同理`OnError`

6. `-XX:ErrorFile=filename`指定Java进程发生不可恢复的错误时，保存错误文件的地址和文件名。默认情况下，错误文件存储在当前路径下，文件名为hs_err_pid[pid].log。一个示例如下（%p表示进程PID）：
    ```
    -XX:ErrorFile=/data/log/java_error_%p.log
    ```
    如果文件由于权限问题、硬盘已满或是其他问题而不能在指定目录下创建，那么该文件将创建在临时目录下。对于Linux环境，临时目录为/tmp；对于Windows环境，临时目录由环境变量TMP指定，如果该环境变量没有指定，那么将使用TEMP环境变量的值。

7. 获取maxDirectMemory的值
  ```java
  public BufferPoolMXBean getDirectBufferPoolMBean(){
    return ManagementFactory.getPlatformMXBeans(BufferPoolMXBean.class)
            .stream()
            .filter(e -> e.getName().equals("direct"))
            .findFirst()
            .orElseThrow();
  }

  public JavaNioAccess.BufferPool getNioBufferPool(){
      return SharedSecrets.getJavaNioAccess().getDirectBufferPool();
  }

  /**
   * -XX:MaxDirectMemorySize=60M
   */
  @Test
  public void testGetMaxDirectMemory(){
      ByteBuffer.allocateDirect(25*1024*1024);
      System.out.println(Runtime.getRuntime().maxMemory() / 1024.0 / 1024.0);
      System.out.println(VM.maxDirectMemory() / 1024.0 / 1024.0);
      // 可以看到BufferPoolMXBean及JavaNioAccess.BufferPool的getMemoryUsed可以返回directBuffer大小
      System.out.println(getDirectBufferPoolMBean().getTotalCapacity() / 1024.0 / 1024.0);
      System.out.println(getNioBufferPool().getTotalCapacity() / 1024.0 / 1024.0);
  }
  ```


### <span style="color:green;">收集器设置<span>


### <span style="color:green;">策略设置<span>
* `-d32`、`-d64`：设置启动模式是32位或64位，-d64模式和-server模式是绑定的，也就是说-d64声明了就默认是server模式。
* 断言：
  ```
  -enableassertions[:[packagename]...|:classname]
  -ea[:[packagename]...|:classname]

  disableassertions[:[packagename]...|:classname]
  -da[:[packagename]...|:classname]
  ```
  系统包下的断言：
  ```
  -enablesystemassertions
  -esa

  -disablesystemassertions
  -dsa
  ```

* 信息打印
  ```
  -verbose:class
  -verbose:gc
  -verbose:jni
  ```

* gc日志：`-Xloggc:filename`
* 关于解释器模式：`-Xmixed`、`-Xint`
* 关闭对class（方法区、元数据区）的gc：`-Xnoclassgc`
* 自行理解`-XshowSettings`
* 错误日志文件：`-XX:ErrorFile=filename`
  > 当不可恢复的错误发生时，错误信息记录到哪个文件。默认是在当前目录的一个叫做hs_err_pid pid.log的文件。如果指定的目录没有写权限，这时候文件会创建到/tmp目录下。使用如下：

* 对象内存对齐大小`-XX:ObjectAlignmentInBytes=alignment`，默认是8字节，JVM实际计算堆内存上限的方法是`4GB * ObjectAlignmentInBytes`；

* 指针压缩` XX:+UseCompressedOops`、`-XX:+UseCompressedClassPointers`、`-XX:+UnlockDiagnosticVMOptions`
  > 对于大小在4G和32G之间的堆，应该使用压缩的oop

* `XX:CompressedClassSpaceSize`（参考：[JVM源码分析之Metaspace解密]）
  > Starting with JDK 8, the permanent generation was removed and the class metadata is allocated in native memory.
  > Java Hotspot VM explicitly manages the space used for metadata. Space is requested from the OS and then divided into chunks. A class loader allocates space for metadata from its chunks (a chunk is bound to a specific class loader).
  > [链接][Class Metadata]

  `CompressedClassSpaceSize指定的内存只保存对象的压缩指针`

* 新生代和survivor大小设置：
  * `-XX:NewRatio=3`
    新生代:老年代=1:3
  * `-Xmn256m`、`-XX:NewSize=256m`、`-XX:MaxNewSize=256m`
  * `-XX:SurvivorRatio=8`
    一个Survivor区:Eden区 = 1:8

* (jdk8之前)永久代
  * `-XX: PermSize=128m`、`-XX:MaxPermSize=512m`
* （jdk8之后）Metaspace
  * `-XX:MetaspaceSize=128m`、`-XX:MaxMetaspaceSize=512m`
* 新生代最大年龄`-XX:MaxTenuringThreshold`，最大值为15，因为对象头中用了4位进行存储垃圾年龄 【1111（二进制）=15（十进制）】;
* 新生代中对象阈值`-XX:PretenureSizeThreshold`，默认0，表示所有对象都保存在Eden中；
* TLAB
  > JVM所占用的主要内存都是从堆空间分配的，堆是所有线程共享的，因此在堆上分配内存需要加锁，Sun JDK为提升效率，会为每个新建的线程在Eden上分配一块独立的空间由该线程独享，这块空间称为TLAB（Thread Local Allocation Buffer）。其大小由JVM根据运行情况计算得到，也可通过参数-XX:TLABWasteTargetPercent来设置TLAB可占用的Eden空间的百分比，默认值为1%。

* `-XX:+DisableExplicitGC`：禁止`System.gc()`执行；参考：[为什么不推荐使用-XX:+DisableExplicitGC]

* `-XX:+PrintStringTableStatistics`[探索StringTable提升YGC性能]

* `-XX:CMSWaitDuration=2000`
  > 触发CMS GC条件比较简单，JVM有一个线程定时扫描Old区，时间间隔可以通过参数-XX:CMSWaitDuration=2000设置（默认就是2s），如果发现Old区占比超过参数-XX:CMSInitiatingOccupancyFraction=75设定值（CMS条件下默认为68%），就会触发CMS GC。

作者：阿飞的博客
链接：https://www.jianshu.com/p/5ace2a0cafa4
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### <span style="color:green;">更多<span>
##### 1. 查看默认值
命令：`java -XX:+PrintFlagsFinal -version`

##### 2. 命令行参数格式
* `-Dproperty=value`
JVM全局属性设置，可通过`System.getProperty("name")`获取；

* 前缀`-X`
表示：HotSpot实现常用功能的选项，其他JVM不一定实现。如：`-Xmx`、`-Xmn`等

* Boolean型设置
布尔Boolean型，表示开启或关闭某一功能。使用加号+表示开启某一功能，减号-表示关闭某一功能，如：` -XX:+OptionName -XX:-OptionaName`

##### 未解之谜
[javaagent使用指南]
参数`-XshowSettings`
jvm的`attach`模式

##### 更多参考
[Java8 JVM参数解读][]


[Java8 JVM参数解读]:https://developer.aliyun.com/article/368436
[javaagent使用指南]:https://www.cnblogs.com/rickiyang/p/11368932.html
[JVM源码分析之Metaspace解密]:http://lovestblog.cn/blog/2016/10/29/metaspace/
[Class Metadata]:https://docs.oracle.com/javase/9/gctuning/other-considerations.htm#JSGCT-GUID-BFB89453-60C0-42AC-81CA-87D59B0ACE2E
[为什么不推荐使用-XX:+DisableExplicitGC]:https://ezlippi.com/blog/2017/10/why-not-expliclitgc.html 'ExplicitGCInvokesConcurrent配合CMS使用'
[探索StringTable提升YGC性能]:https://www.jianshu.com/p/5524fce8b08f
