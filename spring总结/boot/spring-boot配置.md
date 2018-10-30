# spring-boot配置篇

## bootstrap与application

### 加载顺序

- bootstrap.yml（bootstrap.properties）先加载
- application.yml（application.properties）后加载

----

* bootstrap.yml 用于应用程序上下文的引导阶段
* bootstrap.yml 由父Spring ApplicationContext加载 

### 约定

- bootstrap.yml 可以理解成`系统级别`的一些参数配置，这些参数一般是不会变动的。eg：
  - spring.application.name
  - spring.cloud.config.server.git.uri
  - 一些加密/解密信息
- application.yml 可以用来定义`应用级别`的，如果搭配 spring-cloud-config 使用 application.yml 里面定义的文件可以实现动态替换









## 扩展

### 1. Resource-通配符

```java
ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
//将加载多个模式匹配的Resource  
Resource[] resources = resolver.getResources("classpath*:META-INF/*.LIST"); 
```