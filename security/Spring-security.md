# spring-security研究<small><small><small>[详细教程](http://www.tianshouzhi.com/api/tutorials/spring_security_4/250)</small></small></small>
## 相关注解和类
**注解**
* `@EnableWebSecurity`
* `@EnableGlobalMethodSecurity`
* `@EnableGlobalAuthentication`  

**类**
* `WebSecurityConfigurerAdapter` 实现 `WebSecurityConfigurer` 继承 `SecurityConfigurer`
* 


## 知识点
* 只继承`AbstractSecurityWebApplicationInitializer`类，便可以继承`spring-security`（前提是使用了`spring-mvc`框架）  

    ```java
    import org.springframework.security.web.context.AbstractSecurityWebApplicationInitializer;  

    // 不用任何注解,然后SecurityConfig就会被自动注入到应用中，SecurityConfig需要注解
    public class SecurityWebApplicationInitializer extends AbstractSecurityWebApplicationInitializer {
    
    }
    ```
    `SecurityConfig`需要注解
    ```java
    import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
      
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
        @Override
        protected void configure(AuthenticationManagerBuilder auth) throws Exception {
            auth
                    .inMemoryAuthentication()
                        .withUser("admin").password("admin").roles("user");
        }
    }
    ```
    
* `security`的配置方式可以有2种（都需要`@EnableWebSecurity`注解)：  
    ```
    // spring doc
        Customizations can be made to WebSecurity by extending WebSecurityConfigurerAdapter and exposing it as a 
    Configuration or implementing WebSecurityConfigurer and exposing it as a Configuration. This configuration is 
    imported when using EnableWebSecurity.
  ```
  * 继承`WebSecurityConfigurerAdapter`类
  * 继承`WebSecurityConfigurer`类  
* **过滤器链** 
  * `@EnableWebSecurity`注解中引入了`WebSecurityConfiguration`类（`@Import`），`WebSecurityConfiguration.springSecurity  
  FilterChain()`方法生成过滤器链；
  * `WebSecurityConfiguration`创建`WebSecurity`，然后，`WebSecurity`的`build()`方法创建`FilterChainProxy`(是`Filter`的子类)
  * 根据`servlet3.0`规范，`servlet容器`要负责创建`/META-INF/services/javax.servlet.ServletContainerInitializer`(spring-  
  web包中)中定义的类
  * 过滤器链的注册：`servlet容器`会加载`SpringServletContainerInitializer`类(`ServletContainerInitializer`的子类），  
  `SpringServletContainerInitializer`通过`@HandlesTypes`注解会引入`WebApplicationInitializer`的实现并在`onStartup()`方法中  
  调用`WebApplicationInitializer.onStartup()`方法，而属于`security`的`WebApplicationInitializer`类  
  (`AbstractSecurityWebApplicationInitializer`) 就在这里被执行，`security`的过滤器链就是在  
  `AbstractSecurityWebApplicationInitializer.onStartup()`中被注册的；
* **配置类`WebSecurityConfigurerAdapter`的加载过程**  
  * `SecurityConfigurer`（`WebSecurityConfigurerAdapter`的父接口）被加载后，会先执行`init(securityBuilder)`方法，然后调用  
  `configure(SecurityBuilder)`方法；  
  `WebSecurityConfigurerAdapter`中的2个方法（`configure(AuthenticationManagerBuilder)`和`configure(HttpSecurity)`）就是在  
  `init(securityBuilder)`中执行的