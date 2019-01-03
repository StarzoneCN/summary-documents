# <div style="text-align:center;color:#FF9900">Npm脚本</div>

*此篇记录webpack项目中的npm执行命令相关脚本，参考[博文](http://www.ruanyifeng.com/blog/2016/10/npm_scripts.html)*

### 1. npm 脚本

npm 允许在`package.json`文件里面，使用`scripts`字段定义脚本命令。

#### 1.1 Run

* 查看当前项目的所有 npm 脚本命令:

  ```shell
  npm run
  ```

#### 1.2 分组

脚本中的冒号是分组的意思：

```json
"lint": "npm run lint:js & npm run lint:css & npm run lint:html",
"lint:js": "eslint --some-flag",
"lint:css": "csslint --that-will-change",
"lint:html": "htmllint --how-things-work"
```



### 2. 原理

> npm 脚本的原理非常简单。每当执行`npm run`，就会自动新建一个 Shell，在这个 Shell 里面执行指定的脚本命令。因此，只要是 Shell（一般是 Bash）可以运行的命令，就可以写在 npm 脚本里面。
>
> 比较特别的是，`npm run`新建的这个 Shell，会将当前目录的`node_modules/.bin`子目录加入`PATH`变量，执行结束后，再将`PATH`变量恢复原样。这意味着，当前目录的`node_modules/.bin`子目录里面的所有脚本，都可以直接用脚本名调用，而不必加上路径。

* npm 脚本的退出码，也遵守 Shell 脚本规则。如果退出码不是`0`，npm 就认为这个脚本执行失败：

  ```json
  "test": "echo \"Error: no test specified\" && exit 1",
  ```



### 3. 通配符

支持Ant通配符：

```
? 匹配任何单字符
* 匹配0或者任意数量的字符
** 匹配0或者更多的目录
```



### 4. 传参

向 npm 脚本传入参数，要使用`--`标明：

```json
"lint": "jshint **.js"
```

向上面的`npm run lint`命令传入参数，必须写成下面这样：

```bash
$ npm run lint --  --reporter checkstyle > checkstyle.xml
```

### 5. 执行顺序

如果 npm 脚本里面需要执行多个任务，那么需要明确它们的执行顺序:

* 如果是并行执行（即同时的平行执行），可以使用`&`符号

  ```bash
  $ npm run script1.js & npm run script2.js
  ```

* 如果是继发执行（即只有前一个任务成功，才执行下一个任务），可以使用`&&`符号

  ```bash
  $ npm run script1.js && npm run script2.js
  ```

这两个符号是 Bash 的功能。



### 6. 默认值

npm 脚本由用户提供。但是，npm 对两个脚本提供了默认值：

```json
"start": "node server.js"，
"install": "node-gyp rebuild"
```

上面代码中，`npm run start`的默认值是`node server.js`，前提是项目根目录下有`server.js`这个脚本；

`npm run install`的默认值是`node-gyp rebuild`，前提是项目根目录下有`binding.gyp`文件。

### 7. 简写形式

- `npm start`是`npm run start`
- `npm stop`是`npm run stop`的简写
- `npm test`是`npm run test`的简写
- `npm restart`是`npm run stop && npm run restart && npm run start`的简写

### 8. 变量

通过`npm_package_`前缀，npm 脚本可以拿到`package.json`里面的字段，如：

```json
{
  "name": "foo", 
  "version": "1.2.5",
  "scripts": {
    "view": "node view.js",
    "build": "node build.js"
  }
}
```

那么，变量`npm_package_name`返回`foo`，变量`npm_package_version`返回`1.2.5`：

```javascript
// view.js or build.js
console.log(process.env.npm_package_name); // foo
console.log(process.env.npm_package_version); // 1.2.5
```

上面代码中，我们通过环境变量`process.env`对象，拿到`package.json`的字段值。如果是 Bash 脚本，可以用`$npm_package_name`和`$npm_package_version`取到这两个值。（`$`是Linux中获取变量的方式，window系统中，可以使用`%`，如：`%npm_package_name%`）

#### 8.1 变量属性

`npm_package_`前缀也支持嵌套的`package.json`字段：

```javascript
  "repository": {
    "type": "git",
    "url": "xxx"
  },
  scripts: {
    "view": "echo $npm_package_repository_type"
  }
```

上面代码中，`repository`字段的`type`属性，可以通过`npm_package_repository_type`取到。

#### 2.2 配置变量

npm 脚本还可以通过`npm_config_`前缀，拿到 npm 的配置变量，即`npm config get xxx`命令返回的值。。比如，当前模块的发行标签，可以通过`npm_config_tag`取到：

```json
{
	"view": "echo $npm_config_tag"
}
```

##### 8.2.1 配置变量覆盖

`package.json`里面的`config`对象，可以被环境变量覆盖：

```json
{ 
  "name" : "foo",
  "config" : { "port" : "8080" },
  "scripts" : { "start" : "node server.js" }
}
```

上面代码中，`npm_package_config_port`变量返回的是`8080`。这个值可以用下面的方法覆盖：

```bash
$ npm config set foo:port 80   # TODO 其中的冒号作用不知
```

### 9. 钩子

npm 脚本有`pre`和`post`两个钩子。举例来说，`build`脚本命令的钩子就是`prebuild`和`postbuild`：

```json
{
	"prebuild": "echo I run before the build script",
    "build": "cross-env NODE_ENV=production webpack",
    "postbuild": "echo I run after the build script"
}
```

类似：

```bash
npm run prebuild && npm run build && npm run postbuild
```

应用：

```json
"clean": "rimraf ./dist && mkdir dist",
"prebuild": "npm run clean",
"build": "cross-env NODE_ENV=production webpack"
```

#### 9.1 默认钩子

- prepublish，postpublish
- preinstall，postinstall
- preuninstall，postuninstall
- preversion，postversion
- pretest，posttest
- prestop，poststop
- prestart，poststart
- prerestart，postrestart



#### 9.2 自定义钩子

自定义的脚本命令也可以加上`pre`和`post`钩子。比如，`myscript`这个脚本命令，也有`premyscript`和`postmyscript`钩子。不过，双重的`pre`和`post`无效，比如`prepretest`和`postposttest`是无效的。

> **注意**：`prepublish`这个钩子不仅会在`npm publish`命令之前运行，还会在`npm install`（不带任何参数）命令之前运行。这种行为很容易让用户感到困惑，所以 npm 4 引入了一个新的钩子`prepare`，行为等同于`prepublish`，而从 npm 5 开始，`prepublish`将只在`npm publish`命令之前运行。

#### 9.3 深入理解

npm 提供一个`npm_lifecycle_event`变量，返回当前正在运行的脚本名称，比如`pretest`、`test`、`posttest`等等。所以，可以利用这个变量，在同一个脚本文件里面，为不同的`npm scripts`命令编写代码。请看下面的例子