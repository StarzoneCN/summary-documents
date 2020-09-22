### 消息编码格式
`StringHttpMessageConverter`是`spring`接口`HttpMessageConverter`的默认实现；`HttpMessageConverter`接口有些类似于`Socket/serverSocket`，实现数据的编解码过程；

```xml
<mvc:annotation-driven content-negotiation-manager="contentNegotiationManager">
  <mvc:message-converters register-defaults="true">
    <!-- 将StringHttpMessageConverter的默认编码设为UTF-8 -->
    <bean class="org.springframework.http.converter.StringHttpMessageConverter">
      <constructor-arg value="UTF-8" />
    </bean>
  </mvc:message-converters>
</mvc:annotation-driven>
```
### 设置view形式
```xml
<!-- REST中根据URL后缀自动判定Content-Type及相应的View -->
<!-- 当Content-Type为“application/json”的时候，以json的形式返回 -->
<!-- 当Content-Type为“application/xml”的时候，以xml的形式返回 -->
<bean id="contentNegotiationManager" class="org.springframework.web.accept.ContentNegotiationManagerFactoryBean">
  <property name="mediaTypes" >
    <value>
      json=application/json
      xml=application/xml
    </value>
  </property>
</bean>
```

### \<context:annotation-config/\>
这条配置的作用是式地向Spring容器注册
* AutowiredAnnotationBeanPostProcessor
  支持`@Autowired`、`@Value`，以及`JSR-330`的`@Inject`;
  > NOTE: Annotation injection will be performed before XML injection; thus the latter configuration will override the former for properties wired through both approaches.
  > 注意：注解注入是在xml注入之前执行的，所以如果2种注入方式都配置了，前面的注入会被后面的注入覆盖掉；
* CommonAnnotationBeanPostProcessor
  主要是对`JSR-250`的支持，支持`PostConstruct`和`PreDestroy`，此外还有支持`javax.xml.ws.WebServiceRef`、`javax.ejb.EJB`等；
  > A default CommonAnnotationBeanPostProcessor will be registered by the "context:annotation-config" and "context:component-scan" XML tags. Remove or turn off the default annotation configuration there if you intend to specify a custom CommonAnnotationBeanPostProcessor bean definition!

* PersistenceAnnotationBeanPostProcessor
* RequiredAnnotationBeanPostProcessor

这些后处理器的作用就是处理对应注解的bean；


[关于Spring事务传播机制-NESTED]:https://zhuanlan.zhihu.com/p/148504094
