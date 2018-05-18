# Spring-Session
## 

## 零散知识点
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