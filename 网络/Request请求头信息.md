# Request请求头信息

## X-Forwarded-For

#### 代理转发

> 在 [代理转发](https://zh.wikipedia.org/w/index.php?title=%E4%BB%A3%E7%90%86%E8%BD%AC%E5%8F%91&action=edit&redlink=1)的场景中，你可以通过内部代理链以及记录在网关设备上的IP地址追踪到网络中客户端的IP地址。处于安全考虑，网关设备在把请求发送到外网（[因特网](https://zh.wikipedia.org/wiki/%E5%9B%A0%E7%89%B9%E7%BD%91)）前，应该去除 X-Forwarded-For 字段里的所有信息。这种情况下所有的信息都表现为从内部网络公共IP生成，因此X-Forwarded-For字段中的信息应该是可靠的。 

