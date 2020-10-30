#  <div style="text-align:center;color:#FF9900">Mybatis</div>

### 配置

##### 打印SQL语句

```xml
<settings>
    <!-- 打印查询语句 -->
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

[详细教程][print-sql]

### 参考博客
[MyBatis一级缓存避坑完全指南] | [MyBatis一级缓存在分布式下的坑]


[print-sql]:http://www.mybatis.org/mybatis-3/zh/logging.html
[MyBatis一级缓存避坑完全指南]:http://www.manongjc.com/article/19949.html
[MyBatis一级缓存在分布式下的坑]:https://juejin.im/post/6844904048970170376
