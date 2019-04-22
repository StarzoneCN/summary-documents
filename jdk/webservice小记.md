# <div style="text-align:center;color:#FF9900">WebService</div>
* **@WebService**
  - 设置类对外开放成`Webservice`服务；`@WebService`会将被注解类中的所有`非静态方法`和`非final方法`都提供出去；
  - 服务类中不能没有方法；
* **@WebMethod(exclude=true)**
如果不想某个方法被开放出去，可以使用`@WebMethod(exclude=true)`注解；
* **Endpoint**
端点服务类
  ```java
    // 绑定服务和地址
    Endpoint.publish("http://127.0.0.1:8080/sayHello", new SayHellowIntefaceImpl());
  ```
* **wsimport**
`wsimport -d . wsdl地址` 生成本地class文件
`wsimport -s . wsdl地址` 生成本地java文件
`wsimport -s . -p 包名 wsdl地址` 生成本地java文件,并且指定包名

## WSDL
* **WSDL**
Web Services Description Language(`Web Services`描述语言);
* **portType**
`portType`是`wsdl`最重要的标签，它定义了服务的具体方法；`portType`有四种类型:
  - `One-way` 此操作可接受消息，但不会返回响应;
  - `Request-response` 此操作可接受一个请求并会返回一个响应;
  - `Solicit-response` 此操作可发送一个请求，并会等待一个响应;
  - `Notification` 此操作可发送一条消息，但不会等待响应;
* **UDDI**(Universal Description, Discovery and Integration)
是一种目录服务，企业可以使用它对 Web services 进行注册和搜索;

## CXF
* **Apache CXF** =` Celtix` + `Xfire`；
