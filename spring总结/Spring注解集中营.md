# <div style="text-align:center;color:#FF9900">Spring注解集中营</div>
> *说明：此篇只记录注解相关的比较生僻的知识点，关于注解的基本作用，此篇不予赘述*
### 1. \@Component
#### 1.1 Bean name的值
* 如果配置了value属性，则取value属性为beanName
* 否则，使用默认beanName
  > 默认beanName为`类名`(不包含包路径，并且首字母小写)；如果是内部类，则beanName为`outerClassBeanName.InnerClassName`;


### 2. \@Scope
#### 2.1 proxyMode
[Spring高级装配之Bean的作用域][scopeOfBean]


### 3. \@RedisHash
理解：被`@RedisHash("redisKey")`注解的实体类，保存到redis中的时候会使用`redisKey:实体id`作为redis-hash类型的key保存在redis中，同时创建一个key为`redisKey`的set类型的value（下文：setValue）作为`redisKey:实体id`的索引（index），setValue的内容是每个实体的`id`；

#### 3.1 相关注解
##### 3.1.1 \@Id
> 包路径：org.springframework.data.annotation

但被`@RedisHash`注解的实体类中没有使用`@Id`时，默认使用id字段的值为索引值（`setValue`），如果想自定义索引值（`setValue`），可以使用`@Id`注解自定义字段；

### 4. \@ControllerAdvice
[\@ControllerAdvice详解][]


[scopeOfBean]: https://www.jianshu.com/p/fcdcbaace675 '查看“使用会话和请求作用域”部份'
[\@ControllerAdvice详解]: https://my.oschina.net/zhangxufeng/blog/2222434
