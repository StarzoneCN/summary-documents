# JS实现滚动条滚动到某个位置

[引用博客][blokOfScrollByJs]

### 先贴代码：

```js
 function  test(){ 
    var $objTr = $("#location"); //找到要定位的地方  tr 
    $objTr.css("background-color","lightgray"); //设置要定位地方的css 
    var objTr = $objTr[0]; //转化为dom对象 
    $("#dataDiv").animate({scrollTop:objTr.offsetTop},"slow"); //定位tr 
  } 
```

```html
<body> 
    <h1>定位</h1> 
    <div id="dataDiv" style="overflow-x:hidden;overflow-y:auto;height:400px;width:450px;"> 
      <table id="tableId" style="width:400px;background-color:yellow" align="center" border="0"> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr id="location"><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
        <tr><td>dddddddddddd</td><td>dddddddddd</td><td>ddddddddddddddddd<td/><td>dddddsererfd</td></tr> 
      </table> 
  </div> 
  <input type="button" value="定位到一行" onclick="test()"/> 
 </body> 
```

### Api解释

* **animate** 

  animate() 方法执行 CSS 属性集的自定义动画。 该方法通过CSS样式将元素从一个状态改变为另一个状态。CSS属性值是逐渐改变的，这样就可以创建动画效果。 只有数字值可创建动画（比如 "margin:30px"）。字符串值无法创建动画（比如 "background-color:red"）。 

* **scrollTop** 

  有些情况下,“元素中内容”的高度会超过“元素本身”的高度, scrollTop指的是“元素中的内容”超出“元素上边界”的那部分的高度 

  > 内层元素的高度值300px > 外层元素的高度值200px,因此“外层元素的内容”(也就是“内层元素”)无法完全显示，而外层元素把overflow设置为auto，因此外层元素的右侧会出现竖直滑动条 初始状态下，“内层元素的上边界”和“外层元素的上边界”重合，没有任何内容超过“外层元素的上边界”，此时scrollTop属性的值为0。 当向下拖动滚动块时，超过“外层元素的上边界”的内容会逐渐增多，scrollTop值就等于这些超出部分的高度。 当拖动滚动块到最底部时，“内层元素的下边界”和“外层元素的下边界”重合，此时,超过“外层元素的上边界”的内容的高度=300px-200px=100px，也就是此时的scrollTop值 

* **offsetTop，offsetLeft**等

  * obj.`offsetTop` 指 obj 距离上方或上层控件的位置，整型，单位像素。 
  * obj.`offsetLeft` 指 obj 距离左方或上层控件的位置，整型，单位像素。 
  * obj.`offsetWidth` 指 obj 控件自身的宽度，整型，单位像素。 
  * obj.`offsetHeight` 指 obj 控件自身的高度，整型，单位像素。 

* **offsetParent** 

  offsetParent属性返回一个对象的引用，这个对象是距离调用offsetParent的元素最近的（在包含层次中最靠近的），并且是已进行过CSS定位的容器元素。 如果这个容器元素未进行CSS定位,则offsetParent属性的取值为根元素(在标准兼容模式下为html元素；在怪异呈现模式下为body元素)的引用。当容器元素的style.display 被设置为 "none"时（译注：IE和Opera除外），offsetParent属性 返回null。 





[blokOfScrollByJs]:http://www.bkjia.com/Javascript/1097587.html

