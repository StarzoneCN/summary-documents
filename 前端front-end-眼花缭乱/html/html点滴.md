# <div style="text-align:center;color:#FF9900">Html点滴</div>

* 为了跨浏览器兼容，请使用 [`window.pageXOffset`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/pageXOffset) 和 [`window.pageYOffset`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/pageYOffset) 代替 `window.scrollX` 和 `window.scrollY。`

  如果不能访问，替代方案：

  ```js
  // For scrollX
  (((t = document.documentElement) || (t = document.body.parentNode))
    && typeof t.scrollLeft == 'number' ? t : document.body).scrollLeft
  // For scrollY
  (((t = document.documentElement) || (t = document.body.parentNode))
    && typeof t.scrollTop == 'number' ? t : document.body).scrollTop
  ```




## 1. 元素



## 2. EventSource

参考[博文](http://www.ruanyifeng.com/blog/2017/05/server-sent_events.html)

EventSource（Server-Sent Events ， 简称SSE）可以实现服务器向页面进行消息推送（IE不支持）。

> 1. `Server-sent events (SSE) is a technology where a browser receives automatic updates from a server via HTTP connection. The Server-Sent Events EventSource API is standardized as part of HTML5[1] by the W3C.`

> SSE和WebSocket很相似，但是WebSocket时双向的，而SSE是单向的；

### 2.1 使用

```javascript
var source = new EventSource(url);
```

上面的`url`可以与当前网址同域，也可以跨域。跨域时，可以指定第二个参数，打开`withCredentials`属性，表示是否一起发送 Cookie：

```javascript
var source = new EventSource(url, { withCredentials: true });
```

#### 2.1.1 连接状态

`EventSource`实例的`readyState`属性，表明连接的当前状态。该属性只读，可以取以下值：

```
0：相当于常量EventSource.CONNECTING，表示连接还未建立，或者断线正在重连。
1：相当于常量EventSource.OPEN，表示连接已经建立，可以接受数据。
2：相当于常量EventSource.CLOSED，表示连接已断，且不会重连。
```

#### 2.1.2 事件

* `open`事件

  ```javascript
  source.onopen = function (event) {
    // ...
  };
  
  // 另一种写法
  source.addEventListener('open', function (event) {
    // ...
  }, false);
  ```

* `message`事件

  ```javascript
  source.onmessage = function (event) {
    var data = event.data;
    // handle message
  };
  
  // 另一种写法
  source.addEventListener('message', function (event) {
    var data = event.data;
    // handle message
  }, false);
  ```

* `error`事件

  ```javascript
  source.onerror = function (event) {
    // handle error event
  };
  
  // 另一种写法
  source.addEventListener('error', function (event) {
    // handle error event
  }, false);
  ```

* 自定义事件

  ```javascript
  source.addEventListener('foo', function (event) {
    var data = event.data;
    // handle message
  }, false);
  ```



#### 2.1.3 关闭

`close`方法用于关闭 SSE 连接：

```javascript
source.close();
```

服务端关闭：

> To cancel the connection from a client, it should call method close of the object EventSource. To cancel the connection from a server, it should return Content-Type other than "text/event-stream" or return HTTP status other than 200 OK.



### 2.2 id 字段

数据标识符用`id`字段表示，相当于每一条数据的编号：

```markup
id: msg1\n
data: message\n\n
```

浏览器用`lastEventId`属性读取这个值。一旦连接断线，浏览器会发送一个 HTTP 头，里面包含一个特殊的`Last-Event-ID`头信息，将这个值发送回来，用来帮助服务器端重建连接。因此，这个头信息可以被视为一种同步机制。

### 2.3 retry 字段

服务器可以用`retry`字段，指定浏览器重新发起连接的时间间隔：

```markup
retry: 10000\n
```

种情况会导致浏览器重新发起连接：一种是时间间隔到期，二是由于网络错误等原因，导致连接出错。