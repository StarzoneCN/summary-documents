

### 1. 接口&类
#### 1.1 <span id="BeanDefinitionReader">BeanDefinitionReader</span>
* 包含一个`BeanDefinitionRegistry`，用于保存读取到的`BeanDefinition`；(*Registry*:登记处)
* 有多个`loadBeanDefinitions`的重载方法，用于直接加载给定uri的BeanDefinition；
* 包含`ResourceLoader`（比如：ResourcePatternResolver），用于解析加载对应uri的BeanDefinition；
* 包含一个`ClassLoader`，如果为Null，表示只将beanName和BeanDefinition以key-value的形式保存，不进行`eagerly`实例化；
* 包含一个`BeanNameGenerator`，对没有定义beanName的bean创建beanName；


#### 1.2 <span id="ResourceLoaderAware">ResourceLoaderAware</span>
* 被调用的时机
  ```
  Invoked after population of normal bean properties but before an init callback like InitializingBean's afterPropertiesSet or a custom init-method. Invoked before ApplicationContextAware's setApplicationContext.
  ```

#### 1.3 <span id="ResourceLoader">ResourceLoader</span>
*加载给定地址（String）的资源*
* 主要实现类：[DefaultResourceLoader](#DefaultResourceLoader)
* 主要子接口：`ResourcePatternResolver`

#### 1.4 <span id="DefaultResourceLoader">DefaultResourceLoader</span>
*ResourceLoader的默认实现*
* 包含一个[ProtocolResolver](#ProtocolResolver)的集合，真正加载绝对URI资源的是[ProtocolResolver](#ProtocolResolver);
* 除了通过[ProtocolResolver](#ProtocolResolver)加载资源，还有其他加载方式（如：classpath），详情请查看源码注释；

#### 1.5 <span id="ProtocolResolver">ProtocolResolver</span>
*处理对应Protocol（协议，包括自定义协议）的uri资源*
