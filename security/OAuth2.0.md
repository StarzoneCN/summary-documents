# <div style="text-align:center;color:#FF9900">OAuth2.0</div>

## 1. 概念

### 1.1 Bearer Token

> Bearer Token 是一种 Access Token ;

### 1.1.1 格式

**Bearer XXXXXXXX**

其中 XXXXXXXX 的格式 b64token。

## 2. 主要类

### 2.1 Authentication

经过` AuthenticationManager.authenticate(Authentication) `方法，Authentication就代表认证的主体信息；

一旦请求被认证，Authentication会被保存在SecurityContextHolder管理的线程的SecurityContext中：

> Once the request has been authenticated, the Authentication will usually be stored in a thread-local SecurityContext managed by the SecurityContextHolder by the authentication mechanism which is being used.

也可以不必通过spring-security机制，直接创建一个明确的authentication：

```java
SecurityContextHolder.getContext().setAuthentication(anAuthentication);
```

`authentication.authenticated`属性除非被设置为true，否则，authentication会一直被认证机制拦截；

SpringSecurityOauth默认是关闭`密码模式`，但是当注入了`AuthenticationManager`类后，`密码模式`便被打开；[参考](https://projects.spring.io/spring-security-oauth/docs/oauth2.html "Grant Types")

### 2.2 AuthorizationEndpoint
管理Oauth的授权地址，处理授权请求，默认URL:`/oauth/authorize`

### 2.3 TokenEndpoint
管理分发token，处理token相关请求，默认URL：`/oauth/token`

### 2.4 OAuth2AuthenticationProcessingFilter
鉴权过滤器，从有access_token的request中下载Authentication信息；
资源服务器端；

### 2.5 AuthorizationServerConfigurer
接口，认证服务器需要实现此接口，达到配置效果







## 杂记

* OAuth 2.0 定义Access Token 是 Resource Server 用来认证的唯一方式；

  >  RFC 6749 里面只定义抽象的概念，细节如 Access Token 格式、怎么传到 Resource Server ，以及 Access Token 无效时， Resource Server 怎么处理，都没有定义。所以在 RFC 6750 另外定义了 Bearer Token 的用法。

* Spring-Security-Oauth项目已[废弃](https://projects.spring.io/spring-security-oauth/docs/oauth2.html)
  > The Spring Security OAuth project is deprecated. The latest OAuth 2.0 support is provided by Spring Security. See [the OAuth 2.0 Migration Guide][OAuth 2.0 Migration(迁移) Guide] for further details.


*****
[理解OAuth2.0认证与客户端授权码模式详解] | [OAuth 2 Developers Guide]


[理解OAuth2.0认证与客户端授权码模式详解]:https://segmentfault.com/a/1190000010540911
[OAuth 2.0 Migration(迁移) Guide]:https://github.com/spring-projects/spring-security/wiki/OAuth-2.0-Migration-Guide
[OAuth 2 Developers Guide]:https://projects.spring.io/spring-security-oauth/docs/oauth2.html
