# Spring-Session
### **简介**
> spring-session是spring旗下的一个项目，把servlet容器实现的httpSession替换为spring-session，专注于解决 session管理问题。可简单快速且无缝的集成到我们的应用中。

* **特点**
  1. 多种存储方式：`redis`、`jvm`的`map`、`mongo`、`gemfire`、`hazelcast`、`jdbc`等；
  2. `Restful API`，不依赖于`cookie`。可通过`header`来传递`jessionID `；
  3. `WebSocket`和`spring-session`结合，同步生命周期管理;
* **架构图**：
![架构图][framework]

### **零散知识点**
* `Session`的持久化依赖于服务端的策略，而`Cookie`的持久化则是依赖于本地文件
* **同父域下的`session`共享** [传送门](http://blog.didispace.com/spring-session-xjf-3/)
  ```java
    @Bean
    public CookieSerializer cookieSerializer() {
        DefaultCookieSerializer serializer = new DefaultCookieSerializer();
        serializer.setCookieName("JSESSIONID");
        serializer.setCookiePath("/");
        serializer.setDomainNamePattern("^.+?\\.(\\w+\\.[a-z]+)$");
        return serializer;
    }
  ```
* 分布式`session一致性`方案回顾：
  1. 开源`servlet容器`-`tomcat`提供的`tomcat-redis-session-manager`、`memcached-session-manager`；
  2. 通过`nginx`之类的负载均衡做`ip_hash`，路由到特定的服务器上；

### **深入分析**
* `spring-session`无缝替换应用服务器的`request`大概原理是：
  1. 自定义个`Filter`；
  2. 继承 `HttpServletRequestWrapper` `、HttpServletResponseWrapper` 类，重写`Request`和`Response`；
  3. 将这个`Filter`添加到过滤器链第一的位置；




[framework]:https://img-blog.csdn.net/20170316154920745?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd29qaWFvbGluYWFh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast
