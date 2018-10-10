# <div style="text-align:center;color:#FF9900">iView</div>

## 1. 上手

### 1.1 直接引用

```html
<script type="text/javascript" src="iview.min.js"></script>
<link rel="stylesheet" href="dist/styles/iview.css">
```

### 1.2 webpack + vuejs + iView

1. 首先通过npm安装iview：

   ```shell
   npm install iview --save
   ```

2. 然后在vue-cli脚手架中的main.js文件里面写入：

   ```js
   import iView from 'iview'
   import 'iview/dist/styles/iview.css'
   
   Vue.use（iView）
   ```

#### 1.2.1 [按需引入](https://www.iviewui.com/docs/guide/start#AXYY)

借助插件 [babel-plugin-import](https://github.com/ant-design/babel-plugin-import)可以实现按需加载组件，减少文件体积。首先安装，并在文件 `.babelrc` 中配置：

```shell
npm install babel-plugin-import --save-dev
```

配置`.babelrc`：

```json
// .babelrc
{
  "plugins": [["import", {
    "libraryName": "iview",
    "libraryDirectory": "src/components"
  }]]
}
```

然后这样按需引入组件，就可以减小体积了：

```js
import { Button, Table } from 'iview';
Vue.component('Button', Button);
Vue.component('Table', Table);
```

**特别提醒**：按需引用仍然需要导入样式，即在 **main.js** 或根组件执行 `import 'iview/dist/styles/iview.css`





## 2. 全局配置 [#](https://www.iviewui.com/docs/guide/global)

**transfer**：所有带浮层的组件，是否将浮层放置在 body 内，默认为不设置，详见各组件默认的 transfer 值。可选值为 `true` 或 `false`。

**size**：所有带有 size 属性的组件的尺寸，默认为不设置，详见各组件默认的 size 值。可选值为 `default`、`small` 或 `large`。

```js
Vue.use(iView, {
    transfer: true,
    size: 'large'
});
```

**注**：组件prop中设置的属性，会覆盖全局配置；



## 3. 设置主题

### 3.1 webpack

1. 建一个目录，比如 `my-theme`；

2. 在 新建的目录（my-theme ）下建立一个 less 文件 `index.less`；

3. 在新建的配置文件（index.less）中进行定制化设置：

   ```less
   // Here are the variables to cover, such as:
   @primary-color: #8c0776;
   ```

   完整的变量列表可以查看 [默认样式配置](https://github.com/iview/iview/blob/2.0/src/styles/custom.less)；

4. 在入口文件 `main.js` 内导入这个 less 文件：

   ```js
   import Vue from 'vue';
   import iView from 'iview';
   import '../my-theme/index.less';
   
   Vue.use(iView);
   ```










