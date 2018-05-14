# 《Java+TCP／IP+Socket编程》总结

## 相关类
* **TCP**
  * InetAddress
  * NetworkInterface
  * InetSocketAddress
  * Socket(关键是`读写流`)
  * ServerSocket(关键方法`accept()`)
* **UDP**
  * DatagramPacket
  * DatagramSocket(关键方法`send(datagramPacket)`和`recieve(datagramPacket)`)
## 知识点
* TCP/IP协议簇中的主要的socket类型：
  * 流套接字（stream socket）
  * 数据包套接字（datagram socket）
* 一个数据包（`DatagramPacket`）最多可以接收65507个字节（加上其他传输控制字节，总共65600）

  
## 原理
* **客户端**  
发起请求（创建一个`socket`实例）  
**服务端**  
通过`ServerSocket`监听tcp请求，并为每一个求情创建`socket`实例
* 构造函数`Socket()`是创建一个没有连接的`socket`，在进行通信之前必须显示地连接它（`connect()`函数）

## 埋坑
* `DatagramPacket`的构造函数中有一个`offset`的参数，使用`getData()`方法时应当注意`offset`的值；（详细描述：查看书籍page40）