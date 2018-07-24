# Java-Json类库性能比较

[博客原文][json-performance-analysis]

### 比较结果

* 序列化（转object → json-string）次数比较小的时候，`Gson`性能最好，当不断增加的时候到了100000，Gson明细弱于`Jackson`和`FastJson`， 这时候FastJson性能是真的牛，另外还可以看到不管数量少还是多，Jackson一直表现优异 ；
* 反序列化（string → jsonObject）的时候，Gson、Jackson和FastJson区别不大，性能都很优异；
* *Json-lib*无论是序列号还是反序列化，性能都是最差的（20倍的差距）；

### Maven坐标

```xml
<!-- Json libs-->
<dependency>
    <groupId>net.sf.json-lib</groupId>
    <artifactId>json-lib</artifactId>
    <version>2.4</version>
    <classifier>jdk15</classifier>
</dependency>
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.8.2</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.46</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.4</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.9.4</version>
</dependency>
```







[json-performance-analysis]:https://www.xncoding.com/2018/01/09/java/jsons.html

