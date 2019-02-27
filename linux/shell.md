# <div style="text-align:center;color:#FF9900">Shell</div>
## 1. 语法
### 1.1 重定向
**将命令的结果输出到文件，而不是标准输出（屏幕）**
 `>` 写入文件并覆盖旧文件
 `>>` 加到文件的尾部，保留旧文件内容

### 1.2 反短斜线
**将一个命令的输出作为另外一个命令的一个命令行参数**
*例：* 查找过去24小时修改过的文件，并打包：
```Shell
tar -zcvf lastmod.tar.gz `find . -mtime -1 -type f -print`
```

### 1.3 if
#### 1.3.1 基本结构
```Shell
if 条件; then

elif 条件; then

else

fi
```
#### 1.3.2 if条件
**通常用` [ ] `来表示条件测试。注意这里的空格很重要。要确保方括号的空格**
* `[ -f "somefile" ]` ：判断是否是一个文件
* `[ -x "/bin/ls" ]` ：判断/bin/ls是否存在并有可执行权限
* `[ -n "$var" ]` ：判断$var变量是否有值
* `[ "$a" = "$b" ]` ：判断$a和$b是否相等 ,注意“=”和变量之间要有空格。
> 更多`if`条件表达式语法，请查看`man test`

### 1.4 快捷操作符`&&`和`||`
[参考](https://blog.csdn.net/hudashi/article/details/6877351 '搜搜"快捷操作符"')
*思考*： 可以从`短路`的思路去理解；

### 1.5 case
[参考](https://blog.csdn.net/love__coder/article/details/7262160) | [参考2](https://blog.csdn.net/edei2004/article/details/3693979) | [参考3](https://blog.csdn.net/u010265663/article/details/71190713)

### 1.6 select
### 1.7 while
### 1.8 for
> 关于`$*`，请[参考](https://blog.csdn.net/hudashi/article/details/6877351 '搜索"此时 $* 包含了"')

### 1.9  默认变量
[参考](https://blog.csdn.net/hudashi/article/details/6877351 '搜索"$1, $2"')

### 1.10 shift
[参考](https://www.cnblogs.com/image-eye/archive/2011/08/20/2147153.html)

### 1.11 字符串边界分隔符
[参考](https://blog.csdn.net/hudashi/article/details/6877351 '搜索"Here documents"')

### 1.12 调试
