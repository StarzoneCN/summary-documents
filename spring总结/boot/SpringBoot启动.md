# <div style="text-align:center;color:#FF9900">SpringBoot启动</div>

## 1. spring.factories默认值
*注：项目引入pom如下*
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>com.baomidou</groupId>
        <artifactId>mybatis-plus-boot-starter</artifactId>
        <version>3.0.6</version>
    </dependency>
</dependencies>   
```

### 1.1 <span id="initializers">initializers</span>
`SpringApplication`默认加载的initializers包含如下实现（排序后）：
```
org.springframework.boot.context.config.DelegatingApplicationContextInitializer
org.springframework.boot.context.ContextIdApplicationContextInitializer
org.springframework.boot.context.ConfigurationWarningsApplicationContextInitializer
org.springframework.boot.context.embedded.ServerPortInfoApplicationContextInitializer
org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer
org.springframework.boot.autoconfigure.logging.AutoConfigurationReportLoggingInitializer
```
* SharedMetadataReaderFactoryContextInitializer相关：internalCachingMetadataReaderFactory、CachingMetadataReaderFactoryPostProcessor
* AutoConfigurationReportLoggingInitializer相关：添加监听器AutoConfigurationReportListener、

### 2.2 <span id="ApplicationListeners">ApplicationListeners</span>
`SpringApplication`默认加载的ApplicationListeners包含如下实现（排序后）：
```
org.springframework.boot.context.config.ConfigFileApplicationListener
org.springframework.boot.context.config.AnsiOutputApplicationListener
org.springframework.boot.logging.LoggingApplicationListener
org.springframework.boot.logging.ClasspathLoggingApplicationListener
org.springframework.boot.autoconfigure.BackgroundPreinitializer
org.springframework.boot.context.config.DelegatingApplicationListener
org.springframework.boot.builder.ParentContextCloserApplicationListener
org.springframework.boot.ClearCachesApplicationListener
org.springframework.boot.context.FileEncodingApplicationListener
org.springframework.boot.liquibase.LiquibaseServiceLocatorApplicationListener
```

### 1.3 <span id="SpringApplicationRunListener">SpringApplicationRunListener</span>
`SpringApplication`默认加载的`org.springframework.boot.SpringApplicationRunListener`包含如下实现（排序后）：
```
org.springframework.boot.context.event.EventPublishingRunListener
```

## 更多
### 博文
[打开BeanFactory ignoreDependencyInterface方法的正确姿势][]
[java中什么是bridge method（桥接方法）][]
[JDK 泛型之 Type][]
[【Java】泛型中 extends 和 super 的区别？][]
[Spring 是如何解析泛型 - ResolvalbeType][]



[打开BeanFactory ignoreDependencyInterface方法的正确姿势]:https://www.jianshu.com/p/3c7e0608ff1f
[java中什么是bridge method（桥接方法）]:https://blog.csdn.net/mhmyqn/article/details/47342577
[JDK 泛型之 Type]:https://www.cnblogs.com/binarylei/p/8485609.html
[【Java】泛型中 extends 和 super 的区别？]:https://itimetraveler.github.io/2016/12/27/%E3%80%90Java%E3%80%91%E6%B3%9B%E5%9E%8B%E4%B8%AD%20extends%20%E5%92%8C%20super%20%E7%9A%84%E5%8C%BA%E5%88%AB%EF%BC%9F/
[Spring 是如何解析泛型 - ResolvalbeType]:https://www.cnblogs.com/binarylei/p/10344262.html
