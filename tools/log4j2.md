# <div style="text-align:center;color:#FF9900">log4j2</div>

## 1. Log4j2
### 1.1 简介

log4j2除了支持properties和xml形式的配置外，还添加了对json和yaml的支持：

> Log4J 2 introduces configuration support through JSON and YAML in addition to properties file and XML.

对于四种配置文件的默认加载顺序是（假定当前是`test`环境）：
> 1. Log4j will inspect the "log4j.configurationFile" system property and, if set, will attempt to load the configuration using the ConfigurationFactory that matches the file extension.
> 2. If no system property is set the properties ConfigurationFactory will look for `log4j2-test.properties` in the classpath.
> 3. If no such file is found the YAML ConfigurationFactory will look for `log4j2-test.yaml` or `log4j2-test.yml` in the classpath.
> 4. If no such file is found the JSON ConfigurationFactory will look for `log4j2-test.json` or `log4j2-test.jsn` in the classpath.
> 5. If no such file is found the XML ConfigurationFactory will look for `log4j2-test.xml` in the classpath.
> 6. If a test file cannot be located the properties ConfigurationFactory will look for `log4j2.properties` on the classpath.
> 7. If a properties file cannot be located the YAML ConfigurationFactory will look for `log4j2.yaml` or `log4j2.yml` on the classpath.
> 8. If a YAML file cannot be located the JSON ConfigurationFactory will look for `log4j2.json` or `log4j2.jsn` on the classpath.
> 9. If a JSON file cannot be located the XML ConfigurationFactory will try to locate `log4j2.xml` on the classpath.
> 10. If no configuration file could be located the `DefaultConfiguration` will be used. This will cause logging output to go to the console.

### 1.2 进阶
#### 1.2.1 配置修改后自动应用
`log4j2`可以通过`monitorInterval`配置项启用此项功能，如果`monitorInterval`为非零值（`interval`），则`log4j2`会在`interval`之后的下一个`logEvent`的时候进行配置检查，如果配置有修改就应用修改；`interval`最小值为`5`；
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration monitorInterval="30">
...
</Configuration>
```

#### 1.2.2 文件删除策略
请参考[Log Archive Retention Policy: Delete on Rollover][]


## 2. 实战

### 2.1 SpringBoot

#### 2.1.1 properties文件

> classes路径下添加`log4j2.properties`即可自动识别；

内容示例：

```properties
status = info
name = PropertiesConfig

filters = threshold

filter.threshold.type = ThresholdFilter
filter.threshold.level = debug

appenders = console

appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %d{yyyy-MM-dd HH:mm:ss.SSS} %-5p %c{1}:%L - %m%n

rootLogger.level = error   # 如果不配置，默认是error
rootLogger.appenderRefs = console
rootLogger.appenderRef.stdout.ref = STDOUT
```



##### <a id="2.1.1.1_maven依赖">2.1.1.1 maven依赖</a>

```xml
!-- 忽略自带的日志框架. -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter</artifactId>
   <exclusions>
       <exclusion>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-logging</artifactId>
      </exclusion>
   </exclusions>
</dependency>     
<!-- log4j2 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

#### 2.1.2 json配置

1. 在classpath路径下添加json文件，如：

   ```json
   {
     "configuration": {
       "status": "error",
       "name": "JSONConfigDemo",
       "packages": "com.howtodoinjava",
       "ThresholdFilter": {
         "level": "debug"
       },
       "appenders": {
         "Console": {
           "name": "STDOUT",
           "PatternLayout": {
             "pattern": "%d [%t] %-5p %c - %m%n"
           }
         }
       },
       "loggers": {
         "root": {
           "level": "error",
           "AppenderRef": {
             "ref": "STDOUT"
           }
         }
       }
     }
   }
   ```

2. 在application.properties或application.yml中指定log4j2配置文件位置，如：

   ```yaml
   logging:
     config: classpath:log4j2.json
   ```

##### 2.1.2.1 maven依赖

同[2.1.1.1 maven依赖](#2.1.1.1_maven依赖)相同；

#### 2.1.3 yaml配置

在classpath中添加`log4j2.yaml`文件，springBoot会自动识别：

```yaml
Configutation:
  name: Default

  Properties:
    Property:
      name: log-path
      value: "logs"

  Appenders:

    Console:
      name: Console_Appender
      target: SYSTEM_OUT
      PatternLayout:
        pattern: "[%-5level] %d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %c{1} - %msg%n"

    File:
      name: File_Appender
      fileName: ${log-path}/logfile.log
      PatternLayout:
        pattern: "[%-5level] %d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %c{1} - %msg%n"

  Loggers:

      Root:
        level: error
        AppenderRef:
          - ref: Console_Appender

      Logger:
        - name: guru.springframework.blog.log4j2yaml
          level: debug
          AppenderRef:
            - ref: File_Appender
              level: error
```

##### 2.1.3.1 maven依赖

yaml需要添加额外的包支持：

```xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-yaml</artifactId>
    <version>2.9.8</version>
</dependency>

<!-- 这个包会被springboot-web引入 -->
<dependency>
   <groupId>com.fasterxml.jackson.core</groupId>
   <artifactId>jackson-databind</artifactId>
   <version>2.5.4</version>
</dependency>
```



## 3. 实战分析

### 3.1 Rolling File

```yaml
RollingFile:
      - name: RollingFile_Appender
        fileName: ${log-path}/rollingfile.log  # 日志文件位置和文件名
        filePattern: "logs/archive/rollingfile.log.%d{yyyy-MM-dd-hh-mm}.gz"
        PatternLayout:
          pattern: "[%-5level] %d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %c{1} - %msg%n"
        Policies:
          SizeBasedTriggeringPolicy:
            size: 1 KB  # 大于1k时，roll日志文件，单位也可以是MB/GB
          DefaultRollOverStrategy:
            max: 30  # 总文件数大于30时，会删除日志文件
```

#### 3.1.1 More

更多[RollingAppender](https://logging.apache.org/log4j/2.x/manual/appenders.html#RollingFileAppender)

> For example, two web applications in a servlet container can have their own configuration and safely write to the same file if Log4j is in a ClassLoader that is common to both of them.
>
> 两个应用打印到一个日志文件；

一个RollingAppender需要2方面配置：

> A RollingFileAppender requires a [TriggeringPolicy](https://logging.apache.org/log4j/2.x/manual/appenders.html#TriggeringPolicies) and a [RolloverStrategy](https://logging.apache.org/log4j/2.x/manual/appenders.html#RolloverStrategies).
>
> * 如果RolloverStrategy没配置，使用 [DefaultRolloverStrategy](https://logging.apache.org/log4j/2.x/manual/appenders.html#DefaultRolloverStrategy)；
> *  a [custom delete action](https://logging.apache.org/log4j/2.x/manual/appenders.html#CustomDeleteOnRollover) can be configured in the DefaultRolloverStrategy to run at rollover.  ——  `Since log4j-2.5`
> *  if no file name is configured then [DirectWriteRolloverStrategy](https://logging.apache.org/log4j/2.x/manual/appenders.html#DirectWriteRolloverStrategy) will be used instead of DefaultRolloverStrategy.  —— `Since 2.8`
> * File locking is not supported by the RollingFileAppender.

##### 3.1.1.1 参数列表

* **append**

  默认true，是否在日志文件后拼接，如果为false，则先清空日志文件，再打印

* **bufferedIO**

  是否使用buffer，默认为true，日志记录会保存在buffer中，直到buffer满了或设置了即时刷新（immediateFlush is set）才会写入硬盘；

  **性能**：性能测试显示，无论是否设置即时刷新，使用buffer，性能都有很大提升；

* **bufferSize**

  如果bufferedIO为true，此项表示buffer大小；默认8k；

* **createOnDemand**

  是否按需创建日志文件：是否在日志事件通过了所有filter并到达appender时，才创建日志文件；默认false；

  > The appender creates the file on-demand. The appender only creates the file when a log event passes all filters and is routed to this appender. Defaults to false.

* **CompositeFilter/filter**

  [参考](https://blog.csdn.net/chenhaotong/article/details/50487557)

* **fileName**

  文件名，当文件不存在时，会自动创建；

* **filePattern**

  日志文件归档形式；取决于使用的RolloverPolicy；默认是DefaultRolloverPolicy；（*TODO - 比较复杂，有时间再细化*）

* **immediateFlush**

  是否及时刷新buffer；

* **layout**

  默认`%m%n`

* **policy**

  类型：TriggeringPolicy

  触发策略；

* **strategy**

  类型：RolloverStrategy

### 3.2 添加appender引用

```yaml
Loggers:

    Root:
      level: debug
      AppenderRef:
        - ref: Console_Appender

    Logger:
      - name: guru.springframework.blog.log4j2yaml
        level: debug
        AppenderRef:
          - ref: File_Appender
            level: error
          - ref: RollingFile_Appender
            level: debug
```

### 3.3 additivity

作用：是否在父级appender中再打印一次；

示例：

```yaml
logger:
  - name: com.example.demo.multi.springBoot.controller
    additivity: true
    level: debug # 打印debug及以上级别日志
    AppenderRef:
      - ref: Console_Appender
        level: info # 打印info及以上级别日志
```

上面的配置，会将info以上级别的日志打印2遍，debug级别的日志打印1遍；

```yaml
logger:
  - name: com.example.demo.multi.springBoot.controller
    additivity: false
    level: debug  # 这里就不打印了
    AppenderRef:
      - ref: Console_Appender
        level: info  # 打印info及以上级别日志
```

上面的配置，会将info以上级别的日志打印1遍；



## 其他

### 参考博客

[log4j2介绍及示例](https://howtodoinjava.com/log4j2/) - <small>推荐</small>

[log4j2yaml配置](https://springframework.guru/log4j-2-configuration-using-yaml/) - <small>推荐</small>



[Log Archive Retention Policy: Delete on Rollover]: https://logging.apache.org/log4j/2.x/manual/appenders.html#Log_Archive_Retention_Policy:_Delete_on_Rollover
