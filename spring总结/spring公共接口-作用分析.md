# 接口作用浅析
* **`ImportAware`**  
  * 与`@Import`结合使用，可以获取所定义注解的元数据（metadata）；
  * 具体作用可查看`@EnableWebSecurity`中的`@Import(WebSecurityConfiguration.class)`；
* **`ImportSelector`**
  * 参考`@EnableWebSecurity`的`@Import`中的`SpringWebMvcImportSelector`
* **`Aware`**  
  * `Aware`没有接口函数，它只是一个标志接口，表示：`spring框架`会通过回调（`call-back`)的方式调用实现了`Aware`接口的`spring bean`
  * `ApplicationContextAwareProcessor`就执行了许多接口的回调，如`EnvironmentAware`、`EmbeddedValueResolverAware`、`ResourceLoaderAware`、`ApplicationEventPublisherAware`、`MessageSourceAware` 、`ApplicationContextAware`等
* **`BeanClassLoaderAware`**  
  * 此类中的`setBeanClassLoader`方法会被回调，当前**`beanFactory`**的`classLoader`将被设置；