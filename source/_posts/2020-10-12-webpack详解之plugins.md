---
title: webpack详解之plugins
date: 2020-10-12 16:09:38
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

使用`plugins`定制多种方式的构建过程，`webpack`提供了多种内置插件，也可以根据自己的需求[编写插件](https://webpack.js.org/contribute/writing-a-plugin/)。插件属性的值是一个数组

```javascript
var webpack = require('webpack');
// importing plugins that do not come by default in webpack
var DashboardPlugin = require('webpack-dashboard/plugin');

// adding plugins to your configuration
module.exports = {
  //...
  plugins: [
    new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/),
    // compile time plugins
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': '"production"',
    }),
    // webpack-dev-server enhancement plugins
    new DashboardPlugin(),
    new webpack.HotModuleReplacementPlugin(),
  ]
};
```

<!-- more -->

#### 常用插件

| 插件                                                         | 描述                                             |
| ------------------------------------------------------------ | ------------------------------------------------ |
| [bannerPlugin](http://0kv30q.coding-pages.com/2020/10/13/webpack%E5%B8%B8%E7%94%A8%E6%8F%92%E4%BB%B6%E4%B9%8BbannerPlugin/) | 在每个`chunk`顶部添加一个`banner`标识            |
| [CommonsChunkPlugin](http://0kv30q.coding-pages.com/2020/10/13/webpack%E5%B8%B8%E7%94%A8%E6%8F%92%E4%BB%B6%E4%B9%8BCommonsChunkPlugin/) | 提取`多个入口`共享的公用模块                     |
| [CompressionWebpackPlugin](http://0kv30q.coding-pages.com/2020/10/15/webpack常用插件之CompressionWebpackPlugin/) | 为`Content-Encoding`提供压缩版本                 |
| [ContextReplacementPlugin](http://0kv30q.coding-pages.com/2020/10/15/webpack%E5%B8%B8%E7%94%A8%E6%8F%92%E4%BB%B6%E4%B9%8BContextReplacementPlugin/) | 替换解析`使用表达式方式引用模块`的规则           |
| CopyWebpackPlugin                                            | 将已经存在的`单个文件`或`整个目录`复制到构建目录 |
| DefinePlugin                                                 | 创建可在编译时配置的全局常量                     |

#### 参考

[webpack document Plugins](https://webpack.js.org/configuration/plugins/)






