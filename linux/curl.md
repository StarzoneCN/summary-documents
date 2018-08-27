# <div style="text-align:center;color:#FF9900">curl</div>

## 1. 应用

### 1. 1 示例

* curl -w \<format\> 192.168.153.1  -  按format格式输出到stdout，format可以是
  * @filename - 读取文件filename中填写的格式

    * 如：curl -w `@/root/data/curl.w.format` 192.168.153.1/hello

  * @-   -  从stdin中读取格式

  * %{variable} - 输出变量值
    * curl -w `%{http_code}` 192.168.153.1/hello
    * `%%` - 输出字符%
    * %在win32环境中是特殊字符，所以必须使用`%%`代替%（输出字符%写法应该就变成%%%%，没尝试）
    * 允许使用的变量：content_type、http_code（http返回码）、local_ip、local_port、num_connects、num_redirects、redirect_url（当没使用-L参数时，输出redirect的目的地）、remote_ip、remote_port等（更多变量，请使用`man`命令，搜索`-w`）

    **注**：如果-w使用多次，只执行最后一次

* curl -x 192.168.100.100:1080 www.mi.com   - 使用代理

* curl -c cookiec.txt  http://www.mi.com   - 保存cookie信息

* curl -e "www.mi.com" http://mall.mi.com   - 防止“防盗链”拦截

## 2. 格式

**curl [options ...] url**

### 2.1 option

* -o/O  - 文件下载

* -r   - 端点续传
* ftp下载文件
  * curl -O -u 用户名:密码 ftp://url
  * curl -O ftp://用户名:密码@url
* -#   - 显示下载进度条
* -s   - 不显示下载进度信息

## 3. 参考博客

* [Linux 指令详解 curl 文件传输](https://blog.csdn.net/doiido/article/details/44015107)

## 



## N. 原理

原理就先留着吧，涉及到libcurl以及源码和linux太底层的技术等鬼东西，时间有限，有机会再一探究竟。。。

