# <div style="text-align:center;color:#FF9900">Nginx</div>

## 原理

### Nginx的进程模型

> 在工作方式上，Nginx分为单工作进程和多工作进程两种模式。
>
> - 在单工作进程模式下，除主进程（master）外，还有一个工作进程（worker），工作进程是单线程的；
>   - 解读：就是总共2个进程，但是每个进程只在一个线程上运行；
> - 在多工作进程模式下，每个工作进程包含多个线程。Nginx默认为单工作进程模式。

理解：默认一个主进程、一个工作进程（单线程的），如果设置多工作进程模式，那么会有多个工作进程（多线程的）

* > 在一些磁盘使用和CPU负载模式，应调整nginx工作(`worker`)的数量。 在这里说一点基础规则：系统管理员应该为其工作负载尝试几个配置。 一般建议可能如下：如果负载模式是CPU密集型的，例如，处理大量TCP/IP，执行SSL或压缩，则nginx工作(`worker`)的数量应与CPU内核数量相匹配; 如果负载大多是磁盘I/O绑定，例如，从存储或重代理服务不同的内容集合 - 工作(`worker`)的数量可能是核心数量的一到两倍。有些工程师会根据个人存储单元的数量选择工作(`worker`)的数量，但这种方法的效率取决于磁盘存储的类型和配置。

* 所有进程主要使用`共享内存机制`进行进程间通信

* 一些特殊模式下还会有一些特殊进程，比如`缓存加载器`和`缓存管理器`

### 并发请求竞争

当我们提供80端口的http服务时，一个连接请求过来，每个进程都有可能处理这个连接，怎么做到的呢？

> 首先，每个worker进程都是从master进程fork过来，在master进程里面，先建立好需要listen的socket（listenfd）之后，然后再fork出多个worker进程。所有worker进程的listenfd会在新连接到来时变得可读，为保证只有一个进程处理该连接，所有worker进程在注册listenfd读事件前抢accept_mutex，抢到互斥锁的那个进程注册listenfd读事件，在读事件里调用accept接受该连接。

* `mutex` - Mutual exclusion 互斥锁

> 首先，Nginx 的处理得先打开 accept_mutex 选项，此时，只有获得了 accept_mutex 的进程才会去添加accept事件，也就是说，Nginx会控制进程是否添加 accept 事件。Nginx 使用一个叫 ngx_accept_disabled 的变量来控制是否去竞争 accept_mutex 锁。在第一段代码中，计算 ngx_accept_disabled 的值，这个值是 Nginx 单进程的所有连接总数的八分之一，减去剩下的空闲连接数量，得到的这个 ngx_accept_disabled 有一个规律，当剩余连接数小于总连接数的八分之一时，其值才大于 0，而且剩余的连接数越小，这个值越大。再看第二段代码，当 ngx_accept_disabled 大于 0 时，不会去尝试获取 accept_mutex 锁，并且将 ngx_accept_disabled 减 1，于是，每次执行到此处时，都会去减 1，直到小于 0。不去获取 accept_mutex 锁，就是等于让出获取连接的机会，很显然可以看出，当空余连接越少时，ngx_accept_disable 越大，于是让出的机会就越多，这样其它进程获取锁的机会也就越大。不去 accept，自己的连接就控制下来了，其它进程的连接池就会得到利用，这样，Nginx 就控制了多进程间连接的平衡了。



## 优缺点

[Nginx VS Apache][]

## 命令

* kill -HUP pid

  *从容地重启nginx，一般用来重启nginx，或重新加载配置*

  * 执行过程：发送kill命令道master进程，master加载新配置，master通知老worker进程停止接收请求（处理完正在处理的请求后，就退出），新worker接管请求；

* nginx在0.8版本之后，引入了一系列命令行参数，来方便我们管理。

  > 比如，./nginx -s reload，就是来重启nginx，./nginx -s stop，就是来停止nginx的运行。如何做到的呢？我们还是拿reload来说，我们看到，执行命令时，我们是启动一个新的nginx进程，而新的nginx进程在解析到reload参数后，就知道我们的目的是控制nginx来重新加载配置文件了，它会向master进程发送信号，然后接下来的动作，就和我们直接向master进程发送信号一样了。

  理解：新建一个进程（接手master的工作），解析命令参数，向master发送命令，之后，就是类似上面kill；

* 启动

  * `./nginx`

* 管理命令

  格式：**nginx -s signal**

  * signal可以是：`stop` - 快速关闭、`quit` - 优雅关闭、`reload` - 重载配置、`reopen` - 重新打开日志

## 配置

### 配置文件

* nginx 的配置文件，默认的位置包括：
  * `/etc/nginx/nginx.conf`,
  * `/usr/local/etc/nginx/nginx.conf`，或
  * `/usr/local/nginx/conf/nginx.conf`

###  配置指令

* *如果配置指令包含空格，一定要单引号或双引号括起来*

#### 指令类型

* **普通指令**

  > 每个上下文仅有唯一值。而且，它只能在当前上下文中定义一次。子级上下文可以覆盖父级中的值，并且这个覆盖值只在当前的子级上下文中有效

  ```nginx
  gzip on;
  gzip off; # 非法，不能在同一个上下文中指定同一普通指令2次
  ```

* **数组指令**

  同级上下文中，会追加；子级上下文会覆盖父级上下文配置；

* **行动指令**

  行动是改变事情的指令。根据模块的需要，它继承的行为可能会有所不同

### 具体指令

#### server

* server_name - 指令的值将检测 Host 头(存储着主机域名)

* Nginx 将会按照下列顺序选择虚拟主机（server上下文）：

  1. 匹配sever_name指令的IP-端口主机

  2. 拥有default_server标记的IP-端口主机

     ```nginx
     server {
       listen      *:80 default_server;
       server_name netguru.co;

       return 200 "Hello from netguru.co";
     }
     ```

  3. 首先定义的IP-端口主机

  4. 如果没有匹配，拒绝连接。

* server_name也可以通过正则表达式进行匹配

  如：在一个server块中配置多个站点：

  ```nginx
  server
     {
       listen       80;
       server_name  ~^(www\.)?(.+)$;
       index index.php index.html;
       root  /data/wwwsite/$2;
     }
  ```

  站点的主目录应该类似于这样的结构：

  ```nginx
  /data/wwwsite/domain.com
  /data/wwwsite/nginx.org
  /data/wwwsite/baidu.com
  /data/wwwsite/google.com
  ```

* server_name的正则匹配顺序

  > Nginx 会存储 3 个哈希表：确切的名字，以星号开始的通配符，和以星号结尾的通配符。如果结果不在任何表中，则将按顺序进行正则表达式测试。

  1. 确切的名字
  2. 最长的通配符名称以星号开始，例如“* .example.org”。
  3. 最长的通配符名称以星号结尾，例如“mail.**”
  4. 首先匹配正则表达式（按照配置文件中的顺序）

* [正则表达式注意](http://tengine.taobao.org/nginx_docs/cn/docs/http/server_names.html "搜索'正则表达式名字'")

* 命名的正则表达式捕获组在后面可以作为变量使用：

  ```nginx
  server {
      server_name   ~^(www\.)?(?<domain>.+)$;  #命名捕获组
  
      location / {
          root   /sites/$domain;
      }
  }
  ```

  * PCRE使用下面语法支持命名捕获组：

    * `?<name>`  - 从PCRE-7.0开始支持，兼容Perl 5.10语法
    *  `?'name'`  - 从PCRE-7.0开始支持，兼容Perl 5.10语法
    *  `?P<name>`  - 从PCRE-4.0开始支持，兼容Python语法    

* nginx使用的正则表达式兼容`PCRE`。为了使用正则表达式，虚拟主机名必须以波浪线“~”起始

* **.netguru.co**

  * .netguru.co - 本意就是 `*.netguru.co`

* server_name的`通配符`

  - 通配符名字只可以在名字的`起始处`或`结尾处`包含一个星号，并且星号与其他字符之间用`点`分隔

* 如果一个请求的请求行中包含有host信息，nginx会忽略header中的host，而使用请求行中的host进行server_name匹配

* 如果不允许请求中缺少“Host”头，可以定义如下主机，丢弃这些请求：

   ```nginx
   server {
       listen       80;
       #如果server块中没有定义server_name，nginx使用空名字作为虚拟主机名；nginx 0.8.48版本以下（含）在同样的情况下会使用机器名作为虚拟主机名
       server_name  "";  
       return       444;
   }
   ```

   在这里，我们设置主机名为空字符串以匹配未定义“Host”头的请求，而且返回了一个nginx特有的，非http标准的返回码444，它可以用来关闭连接。

   > 从0.8.48版本开始，这已成为主机名的默认设置，所以可以省略`server_name ""`。而之前的版本使用机器的*hostname*作为主机名的默认值。

* 特殊的server_name - \*

   * [特殊的名字](http://tengine.taobao.org/nginx_docs/cn/docs/http/server_names.html "搜索'特殊的名字'")

* `确切名字`和`通配符名字`存储在哈希表中。哈希表和监听端口关联。哈希表的尺寸在配置阶段进行了优化，可以以最小的CPU缓存命中失败来找到名字。设置哈希表的细节参见[这篇文档](http://tengine.taobao.org/nginx_docs/cn/docs/hash.html) 

#### location

#### try_files

* 应该避免在 server 上下文中出现 try_files - [why](https://www.oschina.net/translate/nginx-tutorial-basics-concepts?lang=chs&page=2#)

#### client_header_buffer_size

> Nginx 会将整个请求头都放在一个 buffer 里面，这个 buffer 的大小通过配置项 client_header_buffer_size 来设置，如果用户的请求头太大，这个 buffer 装不下，那 Nginx 就会重新分配一个新的更大的 buffer 来装请求头，这个大 buffer 可以通过 large_client_header_buffers 来设置，这个 large_buffer 这一组 buffer，比如配置 48k，就是表示有四个 8k 大小的 buffer 可以用。注意，为了保存请求行或请求头的完整性，一个完整的请求行或请求头，需要放在一个连续的内存里面，所以，一个完整的请求行或请求头，只会保存在一个 buffer 里面。这样，如果请求行大于一个 buffer 的大小，就会返回 414 错误，如果一个请求头大小大于一个 buffer 大小，就会返回 400 错误。

> 客户端请求头部的缓冲区大小(client_header_buffer_size)，这个可以根据你的系统分页大小来设置，一般一个请求的头部大小不会超过1k，不过由于一般系统分页都要大于1k，所以这里设置为分页大小。分页大小可以用命令getconf PAGESIZE取得。

* [传送门](http://blog.51cto.com/leejia/1198027)

#### lingering_close

* [传送门](http://blog.51cto.com/blief/1736849)

#### proxy_pass

* [通俗示例](https://blog.csdn.net/zhongzh86/article/details/70173174)

### 日志配置

* [传送门](http://tengine.taobao.org/nginx_docs/cn/docs/debugging_log.html)

## 安装设置   <font size="5">[✈](http://tengine.taobao.org/nginx_docs/cn/docs/install.html)</font>

* 例：在nginx目录下执行，./configure --prefix=../nginx --sbin-path=../nginx/bin/nginx

### 编译时特殊依赖

#### zlib

- `--without-http_gzip_module` — 不编译[http_gzip_module](http://tengine.taobao.org/nginx_docs/cn/docs/http/ngx_http_gzip_module.html)模块。该模块可以压缩HTTP服务器的响应，该模块需要`zlib`库才能编译和运行。
- `--with-zlib=path` — 设置zlib库源文件的路径地址

#### PCRE

- `--without-http_rewrite_module` — 不编译[http_rewrite_module](http://tengine.taobao.org/nginx_docs/cn/docs/http/ngx_http_rewrite_module.html)模块。该模块允许HTTP服务器重定向请求，改变请求的URI地址 。创建并运行该模块需要`PCRE`库支持。
- `--with-pcre=path` — 设置PCRE库源文件的路径地址。PCRE库的发行版(version 4.4 — 8.30)需要先从[PCRE](http://www.pcre.org/)站点下载并解压缩
- `--with-pcre-jit` — 编译PCRE库时增加“实时编译（[pcre_jit](http://tengine.taobao.org/nginx_docs/cn/docs/ngx_core_module.html#pcre_jit)）”支持。
- 配置中使用到的正则匹配主要使用到的就是PCRE库

#### OpenSSL

- `--with-http_ssl_module` — 为HTTP服务器编译[HTTPS协议支持的模块](http://tengine.taobao.org/nginx_docs/cn/docs/http/ngx_http_ssl_module.html)。该模块默认是不编译的。它需要OpenSSL库才能编译和运行。



## 优化

* worker进程数最好和CPU物理核数相同
* 可以绑定worker进程到特定核心上

### server_name

> nginx首先搜索确切名字的哈希表，如果没有找到，搜索以星号起始的通配符名字的哈希表，如果还是没有找到，继续搜索以星号结束的通配符名字的哈希表。
>
> 因为名字是按照域名的节来搜索的，所以搜索通配符名字的哈希表比搜索确切名字的哈希表慢。注意特殊的通配符名字“`.example.org`”存储在通配符名字的哈希表中，而不在确切名字的哈希表中。
>
> 正则表达式是一个一个串行的测试，所以是最慢的，而且不可扩展。
>
> 鉴于以上原因，请尽可能使用确切的名字。





## 博文

[nginx 并发数问题思考：worker_connections,worker_processes与 max clients](http://blog.51cto.com/liuqunying/1420556)





# NGINX COOKBOOK

## 1. 示例

### 1.1 HTTP负载均衡

```nginx
// nginx cookbook P14
upstream backend {
     server 10.10.12.45:80 weight=1;
     server app.example.com:80 weight=2;
}
server {
     location / {
     	proxy_pass http://backend;
     }
}
```

### 1.2 TCP

```nginx
stream {
     upstream mysql_read {
         server read1.example.com:3306 weight=5;
         server read2.example.com:3306; // 默认权重是1
         server 10.10.12.34:3306 backup; // 备用数据库，当主库down掉后，自动启用
     }
     server {
         listen 3306;
         proxy_pass mysql_read;
     }
}

```



## 2 负载均衡

NGINX提供了多种负载均衡算法，这些算法不仅可以直接选用，而且还可以进行定制化设置；

### 2.1 Round robin - 轮询

`Round robin`是nginx默认的均衡算法；有权重（weight）配置；

### 2.2 Least connections

**指令名**： **least_conn**

**作用**：将请求代理到最少连接的服务器；也有权重（weight）配置；

```nginx
upstream backend {
 least_conn;
 server backend.example.com;
 server backend1.example.com;
}

```



### 2.3 Least time

**指令名**：**least_time**

**指令参数**：

1. header - 从服务器接收第一个字节的时间
2. last_byte - 从服务器接收完整响应的时间

**作用**：类似`Least connections`，会将请求代理到最少连接服务器，但是更加倾向于响应时间最短的服务器；

**兼容性**：只有nginx-plus支持；

```nginx
upstream backend {
    least_time header;
    server backend1.example.com;
    server backend2.example.com;
}
```

### 2.4 Generic hash

**指令名**：**hash**

**作用**：可以将request和runtime作为变量，计算hash值，根据hash值进行服务器分配，可以根据请求来源进行控制；

**参数**：`consistent` - 选填，可以最小化请求再分配的概率；

**注意**：在添加和删除服务器的时候，会重新计算hash对request进行分配；

>  Note that when a server is added or removed from the pool, the hashed requests will be redistributed. 

```nginx
upstream backend {
    hash $request_uri consistent;
    server backend1.example.com;
    server backend2.example.com;
}
```

### 2.5 IP hash

**指令名**：ip_hash

**说明**：只支持HTTP，与通用hash(Generic hash)稍微有些区别，ip_hash是取IPv4的前3个字节，或IPv6的整体作为hash算法的参数；这种方式可以保证同一IP始终访问同一个server，对没有进行session共享的分布式系统很有帮助；也支持权重（weight）设置；

```nginx
upstream backend {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
}
```

如果需要从负载平衡轮换中临时删除其中一个服务器，则可以使用 **down** 参数对其进行标记，以便保留客户端IP地址的当前哈希值。 此服务器要处理的请求将自动发送到组中的下一个服务器：

```nginx
upstream backend {
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com down;
}
```



## 3. 连接限制

### 3.1 max_conns

**兼容性**：nginx-plus

**说明**：限制连接数；

```nginx
upstream backend {
 zone backends 64k;
 queue 750 timeout=30s;
 server webserver1.example.com max_conns=25;
 server webserver2.example.com max_conns=15;
}

```

上面例子说明：

* 每个服务器有最大链接数设置，如果每个服务都达到了最大连接数，那么新的请求会被添加到队列总，等待处理，前提是，指定了队列（即，设置了队列）；
* 750 - 表示队列的容量；
* zone - 共享内存指令，这个共享区可以使所有worker线程知道**每个服务器的连接数**以及**队列中等待处理的请求数**；
* timeout - 队列中请求的超时时间，默认60s；



## 4. session persistence

相关指令：`sticky`

**兼容性**：nginx-plus

### 4.1 实现方式

nginx-plus有3中session追踪的方式：

1. 创建并追踪nginx自己的cookies；
2. 监测server创建cookies；
3. 基于运行时变量（runtime variable）进行路由；



### 4.2 Sticky Cookie

```nginx
upstream backend {
 server backend1.example.com;
 server backend2.example.com;
 sticky cookie
 affinity
 expires=1h
 domain=.example.com
 httponly
 secure
 path=/;
}
```

例子说明：

> This configuration creates and tracks a cookie that ties a downstream client to an upstream server. The cookie in this example is named affinity, is set for example.com, persists an hour, cannot be consumed client-side, can only be sent over HTTPS, and is valid for all paths.

大致翻译：创建并追踪cookie，实现客户端下行流与服务端上行流的绑定；（例子）为*.example.com创建了一个名字为affinity，持续时长为1小时，不能被客户端的cookie使用，只能发送https请求，面向所有路径的cookie；

#### 4.2.1 指令分析

1. nginx-plus能够通过追踪cookie将后续的请求分发到同一个服务器；
2. 参数cookie的第一个参数是待新建并追踪的cookie的名字；



### 4.3  Sticky Learn

**涉及指令**：sticky learn

**作用**：发现并追踪服务器应用创建的cookie，达到客户端与服务器关联的目的；

```nginx
upstream backend {
 server backend1.example.com:8080;
 server backend2.example.com:8081;
    
 sticky learn
 create=$upstream_cookie_cookiename
 lookup=$cookie_cookiename
 zone=client_sessions:2m;
}
```

**例子解析**：nginx会监测response，获取cookie并保存在大小为2M的内存共享区（大约可保存16000个session），同时监测request，如果request中存在cookie并与response中的一致，nginx就会根据共享区保存的cookie信息分发请求到指定服务器；

**参数说明**：

1. `zone` - 内存共享区名字（上述例子中的client_sessions）和大小

2. `create` -  查询服务器返回（response）中cookie名的字段key；

3. `lookup` - request中与create匹配的cookie名；

4. `upstream_cookie_cookiename`、`cookie_cookiename` - http模块暴露出来的变量；

   >  The value of these parameters are variables exposed by the HTTP module.    (Page 11)



### 4.4 Sticky Routing

**涉及指令**： sticky route 、 [map](http://www.ttlsa.com/nginx/using-nginx-map-method/ "map指令讲解")

```nginx
map $cookie_jsessionid $route_cookie {
 ~.+\.(?P<route>\w+)$ $route;
}
map $request_uri $route_uri {
 ~jsessionid=.+\.(?P<route>\w+)$ $route;
}
upstream backend {
 server backend1.example.com route=a;
 server backend2.example.com route=b;
 sticky route $route_cookie $route_uri;
}
```

**示例解析**：

1. `sticky route`后可添加任意数量的变量，比如，此例中有2个变量`$route_cookie`和`$route_uri`，这些变量中的第一个非零非空的值将被`sticky route`指令使用；
2. 此例中，如果jsessionid被使用，则route到backend1，如果uri被使用，则route到backend2；

另外：

> Essentially, the sticky route directive creates a session within the NGINX Plus shared memory zone for tracking any client session identifier you specify to the upstream server, consistently delivering  requests with this session identifier to the same upstream server as its original request.



### 4.5  Connection Draining

**涉及指令参数**：drain（是server指令的参数）

**说明**：如果server被添加了drain参数，nginx-plus会停止向server发送新的request，已发送到server的request会继续直到处理完；



## 5. 服务降级防止措施

可能因为服务器、网络、应用健壮性等原因导致服务不可达（服务降级）。一种处理方式就是使用代理去监控检测servers的健康状况；nginx提供了2种类型的健康检测：

1. 被动方式
2. 主动方式

### 5.1 passive

**兼容版本**：开源版本

nginx检测所有server的connection和response，判断server是否正常；

**优点**：负载低

### 5.2 active 

**兼容版本**：nginx-plus

nginx会定期主动创建connection到server，并分析response是否正确；

**优点**：及早发现server问题，不必等到client请求的时候才发现问题；

### 5.3 Slow Start

**涉及指令参数**：slow_start （server指令参数）

**作用**：设置在server添加到server池中后的一段时间内缓慢地增加连接数；

```nginx
upstream {
 zone backend 64k;
    
 server server1.example.com slow_start=20s;
 server server2.example.com slow_start=15s;
}
```

`slow_start`指令是的应用有时间去填充cache，初始化数据库连接；这个指令对server失败后又重新加入到server池的情况比较有帮助；



### 5.4  TCP Health Checks

>  check upstream TCP server for health and remove unhealthy servers from the server pool.

**相关指令参数**：health_check （server指令参数）

```nginx
stream {
 server {
   listen 3306;
   proxy_pass read_backend;
   health_check interval=10 passes=2 fails=3;
 }
}
```

**例子解析**：主动模式，nginx每10秒检测一次，如果有3次甚至更多响应失败，那么nginx就认为server是unhealthy；只有2此通过healthCheck才算healthy；

**兼容性**：nginx-plus可以对TCP进行主动和被动2中模式的检测；

* 被动模式：检测client和server的交互，判断是否healthy；如果超时或拒绝连接，表示unhealthy；
* 主动模式：不仅仅测试与server的链接，还可以通过判断response是否符合期望来判断健康性；



### 5.5 HTTP Health Checks

**相关指令**：health_check  （location块中）

```nginx
http {
 server {
 ...
     location / {
         proxy_pass http://backend;
         health_check 	interval=2s
         				fails=2
         				passes=5
         				uri=/
         				match=welcome;
     }
 }
 # status is 200, content type is "text/html",
 # and body contains "Welcome to nginx!"
 match welcome {
     status 200;
     header Content-Type = text/html;
     body ~ "Welcome to nginx!";
 }
}
```

**示例解析**：

* `health_check`检测接口是`/`，
* 周期为2s，
* 每2s要有5次通过检测才算healthy，失败2次就算unhealthy，
* response必须匹配名字叫welcome的`match`块
  * 返回码 - 200；
  * 响应头参数Content-Type的值为`text/html`;
  * 响应体为“Welcome to nginx!”

## 6. 高可用（HA）

**兼容性**：nginx-plus， 需要安装`nginx-ha-keepalived`包

> Use NGINX Plus’s HA mode with keepalived by installing the nginx-ha-keepalived package from the NGINX Plus repository. 

这个HA包是基于keepalived的，管理着一个暴露给客户端的virtual IP：

> This package, based on keepalived, manages a virtual IP address exposed to the client.

会有一个额外的程序在nginx server上运行，确保nginx-plus和keepalived的正常运行：

> Another process is run on the NGINX server that ensures that NGINX Plus and the keepalived process are running. 

> **Keepalived**:
>
> Keepalived采用虚拟路由器冗余协议（VRRP - Virtual Router Redundancy Protocol），向备份服务器发送心跳，如果备份服务器连续3次（three consecutive periods）没有收到心跳，备份服务器就会启动，并将虚拟IP地址（ the virtual IP address）转移到自己（备份服务器）身上，把自己变为master；

### 6.1 Load Balancing on EC2

Page 23



## 7. Content Caching

利用nginx的内容缓存还可以做自己的CDN（content delivery networks 内容分发）；

### 7.1 内容缓存位置

**相关指令**：proxy_cache_path 、proxy_cache

```nginx
proxy_cache_path /var/nginx/cache
 keys_zone=CACHE:60m
 levels=1:2
 inactive=3h
 max_size=20g;

# 关闭cache： proxy_cache off;
proxy_cache CACHE;
```

**示例解析**：

* 在文件系统创建一个responses缓存目录，位置是“/var/nginx/cache”；
* 创建共享内存，命名为“CACHE”，大小为60M;
* levels - 目录结构等级；定义目录如何创建；用冒号分隔，定义每层子目录名字的长度（这里第一层子目录名字长度为1，第二层名字长度为2），子目录最大层数为3层；这些子目录的名字其实都是计算hash值得到的；
* 如果3个小时内没有请求，response将被释放；
* 目录最大容量为20G（20 * 1024 * 1024k）
* proxy_cache指具体使用哪个缓存区；

>  The `proxy_cache_path` is valid in the HTTP context, and the `proxy_cache` directive is valid in the HTTP, server, and location contexts. 

个人理解：内容缓存其实是将request的hash值保存在内存中，真实的response是保存在硬盘中，重复请求的时候是先计算hash值，再通过hash值去硬盘查找response；

### 7.2 Caching Hash Keys

控制内容缓存方式和查找方式；

**相关指令**：proxy_cache_key （http、server、location块）

```nginx
# 计算$host$request_uri$cookie_user的md5作为hash值；
# 默认key：$scheme$proxy_host$request_uri
proxy_cache_key "$host$request_uri$cookie_user";
```

### 7.3 Cache Bypass

**相关指令**：proxy_cache_bypass

**使用方式**：在location块中，设置一个非空非零的变量，然后再使用proxy_cache_bypass指令；变量设置包括但不限于cookie、header和request参数；

```nginx
proxy_cache_bypass $http_cache_bypass;
```

**示例解释**：如果request头（header）中包含变量`cache_bypass`并且值为非0，则此请求不被缓存；

### 7.4  Cache Performance

```nginx
location ~* \.(css|js)$ {
 expires 1y; # 有效期1年
    
 # public表示所有客户端都可以获取缓存，提高性能，避免每个client存一份；
 # private表示每个client私有的，每个client缓存一份；
 add_header Cache-Control "public"; 
}

```









## 说明

* 此文中使用到的页码均为nginx官网下载的《NGINX cookbook》的电子版pdf文件页码；







# 扩展

## 1. 惊群现象

### 1.1 概念

> 如今网络编程中经常用到多进程或多线程模型，大概的思路是父进程创建socket，bind、listen后，通过fork创建多个子进程，每个子进程继承了父进程的socket，调用accpet开始监听等待网络连接。这个时候有多个进程同时等待网络的连接事件，当这个事件发生时，这些进程被同时唤醒，就是“惊群”。这样会导致什么问题呢？我们知道进程被唤醒，需要进行内核重新调度，这样每个进程同时去响应这一个事件，而最终只有一个进程能处理事件成功，其他的进程在处理该事件失败后重新休眠或其他。

### 1.2 nginx的解决方式

> Nginx中使用mutex互斥锁解决这个问题，具体措施有使用全局互斥锁，每个子进程在epoll_wait()之前先去申请锁，申请到则继续处理，获取不到则等待，并设置了一个负载均衡的[算法](http://www.haodaima.net/tag/%E7%AE%97%E6%B3%95)（当某一个子进程的任务量达到总设置量的7/8时，则不会再尝试去申请锁）来均衡各个进程的任务量。后面深入学习一下Nginx的惊群处理过程。



[Nginx VS Apache]:https://blog.csdn.net/liutengteng130/article/details/46700939 "搜索-‘Nginx VSApache’ "
