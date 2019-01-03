# <div style="text-align:center;color:#FF9900">OAuth2.0</div>

## 1. 概念

### 1.1 Bearer Token 

> Bearer Token 是一种 Access Token ;

### 1.1.1 格式

**Bearer XXXXXXXX**

其中 XXXXXXXX 的格式 b64token。



## 杂记

* OAuth 2.0 定义Access Token 是 Resource Server 用来认证的唯一方式；

  >  RFC 6749 里面只定义抽象的概念，细节如 Access Token 格式、怎么传到 Resource Server ，以及 Access Token 无效时， Resource Server 怎么处理，都没有定义。所以在 RFC 6750 另外定义了 Bearer Token 的用法。

* 