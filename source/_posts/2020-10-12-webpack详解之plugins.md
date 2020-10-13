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

| 名称               | 文档链接 |
| ------------------ | -------- |
| bannerPlugin       |          |
| CommonsChunkPlugin |          |
|                    |          |

#### 参考

[webpack document Plugins](https://webpack.js.org/configuration/plugins/)






