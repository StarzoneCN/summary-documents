# <div style="text-align:center;color:#FF9900">Dubbo知识点</div>

### 注意点
1. 参数及返回值不能自定义实现 List, Map, Number, Date, Calendar 等接口，只能用 JDK 自带的实现，因为 hessian 会做特殊处理，自定义实现类中的属性值都会丢失；只传成员属性值和值的类型，不传方法或静态变量[参考](http://dubbo.apache.org/zh-cn/docs/user/references/protocol/dubbo.html)
2. `Dubbo`只有 `group`，`interface`，`version` 是服务的匹配条件，三者决定是不是同一个服务，其它配置项均为调优和治理参数。[参考](http://dubbo.apache.org/zh-cn/docs/user/references/xml/introduction.html)
