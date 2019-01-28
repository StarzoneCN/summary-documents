## replace

插入数据的表必须有主键或者是唯一索引！否则的话，`replace into` 会直接插入数据，这将导致表中出现重复的数据。  

## 时间的国际化

对于`TIMESTAMP`，它把客户端插入的时间从当前时区转化为UTC（世界标准时间）进行存储。查询时，将其又转化为客户端当前时区进行返回。而对于`DATETIME`，不做任何改变，基本上是原样输入和输出；

## 编码集

**ci** - utf8_general_ci中的ci，case insensitive的意思，即 "大小写不敏感"；

**bin** - 可以保存二进制内容, 区分大小写，比如：utf8_bin；

**cs** - case sensitive，区分大小写，如果用户名和邮箱用这个 就会照成不良后果

### utf8_unicode_ci与utf8_general_ci

* 当前，utf8_unicode_ci校对规则仅部分支持Unicode校对规则算法。一些字符还是不能支持。并且，不能完全支持组合的记号。这主要影响越南和俄罗斯的一些少数民族语言，如：Udmurt 、Tatar、Bashkir和Mari。
* utf8_unicode_ci的最主要的特色是支持扩展，即当把一个字母看作与其它字母组合相等时。例如，在德语和一些其它语言中‘ß'等于‘ss'。
* utf8_general_ci是一个遗留的 校对规则，不支持扩展。它仅能够在字符之间进行逐个比较。这意味着**utf8_general_ci校对规则进行的比较速度很快**，但是**与使用utf8_unicode_ci的 校对规则相比，比较正确性较差**）。



## 时区问题

关于新版驱动`com.mysql.cj.jdbc.Driver`，如果没配置服务器时间，会报异常：

```
java.sql.SQLException: The server time zone value 'ÃÃÂ¹ÃºÂ±ÃªÃÂ¼ÃÂ±Â¼Ã¤' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.
```

解决方式：添加`serverTimezone`属性

```java
jdbc:mysql://localhost:3306/4mybatis_plus?seUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
```

