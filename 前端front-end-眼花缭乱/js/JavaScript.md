# JavaScript

## 生僻知识点

#### 变量

* 变量提升
* “use strict”
* 

#### isNAN

[参考博客][isNaN]

```js
isNaN(NaN);       // true
isNaN(undefined); // true
isNaN({});        // true

isNaN(true);      // false
isNaN(null);      // false
isNaN(37);        // false

// strings
isNaN("37");      // false: 可以被转换成数值37
isNaN("37.37");   // false: 可以被转换成数值37.37
isNaN("37,5");    // true
isNaN('123ABC');  // true:  parseInt("123ABC")的结果是 123, 但是Number("123ABC")结果是 NaN
isNaN("");        // false: 空字符串被转换成0
isNaN(" ");       // false: 包含空格的字符串被转换成0

// dates
isNaN(new Date());                // false
isNaN(new Date().toString());     // true

isNaN("blabla")   // true: "blabla"不能转换成数值
                  // 转换成数值失败， 返回NaN
```







[isNaN]:https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/isNaN