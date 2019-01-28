# <div style="text-align:center;color:#FF9900">IDAE优化设置</div>

## settings

### File Encodings

**内容**：设置文件编码

**说明**：

* `Transparent native-to-ascii conversion` - 作用是保存properties文件的时候，以ASCII码形式保存；

**建议**：全部设置成`utf-8`；

### File and Code Templates

**内容**：文件创建模板；

**说明**：

* \#set(\$`NAME`="`VALUE`")   -  变量设置值
* \#parse("Full name")  -  表示引入`Includes`tab中的模板；
* 如果希望在使用的模板的时候，弹出输入提示框，可以直接使用未定义过的变量，idea在运用模板的时候会自动弹出填写提示框，如下面示例中的`DESCRIPTION`；

**示例**：

```java
#set($USER="LiHongxing")
/**
 * ${DESCRIPTION}
 * @author ${USER}
 * @email: lihongxing@bluemoon.com.cn
 * @created: ${DATE} ${TIME}
 * @modefied by
 */
```

更多请查阅[官方文档](https://www.jetbrains.com/help/idea/using-file-and-code-templates.html)

### Code Style

*Editor > Code Style*

**内容**：tab与空格之类的设置；

**说明**：`Use tab character`一般去掉勾选；

### 滚轮调整字体大小

*Editor > General* 的Mouse模块 ：勾选`Change font size (Zoom) with Ctrl + Mouse Wheel`

### Console显示行数

*Editor > General  > Console*: `Override console cycle buffer size` - 设置控制台缓存大小，越大，显示的日志就越多；

### 注释位置

使用双斜线`//`注释快捷键时，双斜线的位置设置：

可以搜索`line comment at first column`， 去掉`line comment at first column（行最前端）`勾选，选中`Add a space at comment start（行第一个字符前空一格）`；

### Others

#### 个人偏好设置

##### 快捷键

* 创建java class ： `Alt + c`
* 创建groovy class： `Alt + g`

