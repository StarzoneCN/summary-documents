# <div style="text-align:center;color:#FF9900">Webpack-插件</div>

## 1. cross-env

*参考[博文](https://blog.csdn.net/qq_26927285/article/details/78105510)*

此插件就是解决`NODE_ENV=development/production`跨平台支持问题；

## 2. uglifyjs-webpack-plugin
代码压缩插件；和`webpack.optimize.UglifyJsPlugin`是同一个插件；


## 3. [SplitChunksPlugin]
[CommonsChunkPlugin]的替代品；
作用：将公共部分提取出来，这样只加载一次，然后缓存在浏览器，后续请求直接读取缓存，如此便可以提高访问性能；


## 4. [CopyWebpackPlugin]
静态文件复制到编译目录`dist`:
```js
// 如下例子：将<i>/static</i>目录下的文件（忽略点号开头的文件）拷贝到编译输出目录
new CopyWebpackPlugin([
      {
        from: path.resolve(__dirname, '../static'), // ${project_home}/static
        to: config.build.assetsSubDirectory,
        ignore: ['.*']
      }
    ])
```









[SplitChunksPlugin]: https://webpack.docschina.org/plugins/split-chunks-plugin/
[CommonsChunkPlugin]: https://webpack.docschina.org/plugins/commons-chunk-plugin/
[CopyWebpackPlugin]: https://webpack.docschina.org/plugins/copy-webpack-plugin/
