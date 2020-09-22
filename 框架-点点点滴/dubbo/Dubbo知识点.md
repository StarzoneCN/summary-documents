# <div style="text-align:center;color:#FF9900">Dubbo知识点</div>

### 注意点
1. 参数及返回值不能自定义实现 List, Map, Number, Date, Calendar 等接口，只能用 JDK 自带的实现，因为 hessian 会做特殊处理，自定义实现类中的属性值都会丢失；只传成员属性值和值的类型，不传方法或静态变量[参考](http://dubbo.apache.org/zh-cn/docs/user/references/protocol/dubbo.html)
2. `Dubbo`只有 `group`，`interface`，`version` 是服务的匹配条件，三者决定是不是同一个服务，其它配置项均为调优和治理参数。[参考](http://dubbo.apache.org/zh-cn/docs/user/references/xml/introduction.html)


### Bug&尝试方案
1. question:dubbo自动关闭，导致服务调用失败；示例[crashDownIssueDemo][]
    ```
    DubboShutdownHook$run -  [DUBBO] Run shutdown hook now., dubbo version: 2.6.6, current host: 10.42.7.28
    ```
    **尝试方案**：暂无
    **暂时参考**：[一次生产问题的排查][]

### 博文
[既然有 HTTP 请求，为什么还要用 RPC 调用？]


[crashDownIssueDemo]:https://github.com/wuqke/crashDownIssueDemo
[一次生产问题的排查]:https://www.twblogs.net/a/5cfed077bd9eee14029f43a4/zh-cn
[既然有 HTTP 请求，为什么还要用 RPC 调用？]:https://www.zhihu.com/question/41609070
