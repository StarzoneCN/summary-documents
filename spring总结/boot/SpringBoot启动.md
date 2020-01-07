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

### 2.2 <span id="listeners">listeners</span>
`SpringApplication`默认加载的listeners包含如下实现（排序后）：
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
