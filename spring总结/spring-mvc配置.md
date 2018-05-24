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
