# <div style="text-align:center;color:#FF9900">Spring-Security</div>

*<small>由于之前写的《Spring-security》比较杂乱，这里重启炉灶！</small>*

## 1. 授权方式

### 1.1 prePostEnabled

打开配置：

```java
@EnableGlobalMethodSecurity(prePostEnabled=true)
```

#### 1.1.1 相关注解

##### @PreAuthorize

> 在方法执行之前执行，而且这里可以调用方法的参数，也可以得到参数值，这是利用JAVA8的参数名反射特性，如果没用JAVA8，那么也可以利用Spring Security的@P标注参数，或者Spring Data的@Param标注参数

> @PreAuthorize is checked on the basis of **role** or the **argument** which is passed to the method.

```java
@PreAuthorize("#userId == authentication.principal.userId or hasAuthority(‘ADMIN’)")
public void changePassword(@P("userId") long userId ){ 

}
```

##### @PostAuthorize

> 在方法执行之后执行，而且这里可以调用方法的返回值，如果EL为false，那么该方法也已经执行完了，可能会回滚。returnObject（EL变量）表示返回的对象。

> @PostAuthorize can be authorized on the basis of logged in **roles**, **return object** (returnObject`) by method and **passed argument** to the method.

```java
@PostAuthorize
public User getUser("returnObject.userId == authentication.principal.userId or hasPermission(returnObject, 'ADMIN')"){

}
```

##### @PostFilter

> 在方法执行之后执行，而且这里可以调用方法的返回值，然后对返回值进行过滤或处理或修改并返回。EL变量returnObject表示返回的对象。只有方法返回的是集合或数组类型的才可以使用。（与分页技术不兼容）

```java
@postFilter
public User getUser("hasPermission(returnObject, 'ADMIN')"){

}
```

##### @PreFilter

> 在方法执行之前执行，而且这里可以调用方法的参数，然后对参数值进行过滤或处理或修改，EL变量filterObject表示参数，如有多个参数，使用filterTarget注解参数。只有方法参数是集合或数组才行。（很少会用到，与分页技术不兼容）

##### 注解使用示例

service层：

```java
package com.concretepage.service;

import org.springframework.security.access.prepost.PostAuthorize;
import org.springframework.security.access.prepost.PreAuthorize;
import com.concretepage.bean.Book;

public interface IBookService {
	@PreAuthorize ("hasRole('ROLE_WRITE')")
	public void addBook(Book book);

	@PostAuthorize ("returnObject.owner == authentication.name")
	public Book getBook();

	@PreAuthorize ("#book.owner == authentication.name")
	public void deleteBook(Book book);
}
```

> authentication and principal keyword can directly be used to access user information. *#* is used to access argument of the method. Take attention on @PostAuthorize, built-in keyword *returnObject* has been used. Here returnObject is equivalent to Book instance returned by the method. [原文](https://www.concretepage.com/spring/spring-security/preauthorize-postauthorize-in-spring-security)

意思：@PreAuthorize和@PostAuthorize都可以直接使用**authentication**和**principal**来获取与用户信息，@PostAuthorize中可以使用关键字**returnObject**，表示方法返回的对象；