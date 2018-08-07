# CSS <font size="1">[CSS参考手册](http://www.w3school.com.cn/cssref/index.asp)</font>

## 基本

* css本身大小写不敏感的，但是涉及到的`HTML`中的`class`和`id`就大小写敏感
* 不可以在内联元素 `<span>` 中嵌入 `<p>` （如果你忘记了原因，请参阅 [XHTML:网站重构](http://www.w3school.com.cn/xhtml/xhtml_structural_01.asp)） 
* `background-color` 不能继承 ，其默认值是*transparent* (透明）
* `background-position`设置数值百分比的时候，可以为负数（如：background-position: -10% 10%;）
* `text-indent` 无法应用到行内元素上
  * `text-indent`会被继承，如果父元素使用了百分数，那么，百分数会被换算成整数，再应用到子元素上



### display

* `dipslay:inline-block` -- 显示为内联块元素，表现为同行显示并可修改宽高内外边距等属性；

* inline是不允许修改宽高等属性的；

* > 内联元素的`margin-left` / `margin-right`及`padding` 可以控制的，所以可以通过这4个属性来控制内联元素的宽度 
  >
  > 内联元素的内部也可以放块级元素标签，而且内部的块级元素标签会撑大外部的内联标签，所以可以通过放块元素来控制内联元素的高度(网上介绍的是内联元素只能放文本及其他内联元素)
  >
  > [引用](https://blog.csdn.net/chen_zw/article/details/8713205)

* col, colgroup, frameset, html, style, table, tbody, tfoot, thead, title, tr, td 等几个元素标签是只读的，也就是不能使用**innerHTML**等进行动态赋值 

#### 内联元素

* 包含：a , abbr , acronym , b , bdo , big , br , cite , code , dfn , em , font , i , img , input , kbd , label , q , s , samp , select , small , span , strike , strong , sub , sup , textarea , tt , u , var



#### 可变元素

*可变元素(根据上下文关系确定该元素是块元素还是内联元素)*

* applet ,button ,del ,iframe , ins ,map ,object , script





## -webkit-

#### -webkit-

* [待续](https://www.cnblogs.com/zhru/p/3832968.html)

  

## min-width

#### min-width

* 

#### min-width相关

* 



## 其他

#### 子元素居父元素底部

* 要使一个节点相对于某个节点固定在指定位置，使用`bottom`（以及top、left和right）属性，但这要求该节点的`position`属性是`absolute`（绝对定位），且其`父节点`的`position`属性为`relative`（相对定位），则该节点即可相对其父节点定位。 
  * eg：父 {position:relative;}   子  {position:absolute;left:0px;bottom:0px;} 

#### position属性

* 包含4个值
  * `relative` 、`absolute` 、`fixed` 、`static` 
* `relative`是相对于元素自己的原点（即左上角顶点）

#### 居中

```css
margin:0 auto; 
```



## 没了



