---
title: webpack详解之entry
date: 2020-09-27 10:55:05
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`entry`用来配置`webpack`入口。`webpack`从入口开始构建，入口可以有多个

#### entry

`entry`用于指定应该程序入口

- 入口是一个文件

```javascript
const entry = {
  main: './src/index.js';
}
// 简写方式
// const entry = './src/index.js';
module.exports = {
  entry
}
```

<!-- more -->

- 入口是一个数组

```javascript
// 传入文件路径数组时，将创建多个主入口，它们将导出到一个chunk
const entry = ['./src/index.js', './src/main.js'];
module.exports = {
  entry
}
```

- 入口包含第三方库入口

```javascript
/*
此方式多用于单入口应用/单页面应用
vendors 主要用于使用CommonsChunkPlugin将bundle中提取vendor代码
注意: 更好的方式是使用DllPlugin
 */
const entry = {
  app: './src/app.js', // 应用程序入口
  vendors: './src/vendors.js' //第三方库入口
};
module.exports = {
  entry
}
```

***总结：***单入口方式主要应用于`单页面应用`，`chunk`会被命名为`main`。缺点是缺乏灵活性

***多个入口***

```javascript
const entry = {
  pageOne: './src/pageOne/index.js',
  pageTwo: './src/pageTwo/index.js',
  pageThree: './src/pageThree/index.js'
};
module.exports = {
  entry
}
```

***总结：***多个入品方式主要用于`多页面应用`，`chunk`的名字为每个`key`的名字。可以使用`CommonsChunkPlugin`提取多个页面共享代码到一个`bundle`

#### 参考

[入口起点](https://www.webpackjs.com/concepts/entry-points/)

[入口和上下文](https://www.webpackjs.com/configuration/entry-context/)