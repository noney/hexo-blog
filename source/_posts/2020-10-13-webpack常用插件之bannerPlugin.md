---
title: webpack常用插件之bannerPlugin
date: 2020-10-13 18:17:41
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`string` `object`

在每个`chunk`顶部添加一个`banner`标识

#### 选项

```javascript
{
  banner: string | function, // 字符串或函数, 它将被包含在一个注释内 如 /*! noneyli */
  raw: boolean, // 值为true， banner不会被包含在注释内
  entryOnly: boolean, // 值为true，banner仅出现在入口chunks
  test: string | RegExp | [string, RegExp], // banner出现在test匹配结果modules中
  include: string | RegExp | [string, RegExp], // banner出现在include包含的modules中
  exclude: string | RegExp | [string, RegExp], // banner不会出现在Exclude包含的modules中
}
```

<!-- more -->

#### 使用

- 值是一个字符串

```javascript
const webpack = require('webpack');
// string
plugins: [
  new webpack.BannerPlugin('noneyli')
]
```

- 值是一个配置对象

```javascript
const webpack = require('webpack');
// object
plugins: [
  new webpack.BannerPlugin({
    banner: (yourVariable) => { return `yourVariable: ${yourVariable}`; }
  })
]
```

#### 占位符

```javascript
import webpack from 'webpack';
new webpack.BannerPlugin({
  banner: 'fullhash:[fullhash], chunkhash:[chunkhash], name:[name], filebase:[filebase], query:[query], file:[file]'
});
```

#### 参考

[webpack document BannerPlugin](https://webpack.js.org/plugins/banner-plugin/)
[webpack中文文档 BannerPlugin](https://www.webpackjs.com/plugins/banner-plugin/)

