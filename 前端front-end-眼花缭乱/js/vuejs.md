# vue.js

#### `vuejs`中`箭头函数`的`this`问题

* 因为箭头函数`() => {}`绑定了`父`上下文，所有`this`并不是想要的当前上下文
* 解决方式：
  * 使用 `functionName: function() {}`
  * 使用`functionName()  {}`