---
title: webpack常用插件之DefinePlugin
date: 2020-10-19 15:53:46
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

定义可在编译阶段使用的全局常量，可以在业务代码中使用。如可以使用它来区分构建环境

```javascript
new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV)
});
```

每个传进`DefinePlugin`的`key`都是一个标识符或用多个`.`连接的标签符（对象）

- 值为字符串，当作代码片段使用
- 值为非字符串，被转换为字符串（包括函数）***结果并非如此，待验证***
- 值为对象，被多个`.`连接访问
- `key`前面添加`typeof `，定义为`typeof`调用

<!-- more -->

```javascript
  new webpack.DefinePlugin({
    wa: 1,
    wb: 'b',
    wc: () => 'c',
    wd: {
      d:1
    },
    wf: ['a', 'b'],
    wg: true,
    'typeof window': JSON.stringify('object1')
  })
```

```javascript
// index.js
console.log(wa, wb, wc, wd, wd.d, wf, wf[0], wg, typeof window); // 1, b, (() => 'c'), ({"d":1}), 1, [a,b], a, true, "object1"
```

***注意：***

- 我们需要使用`process.env.NODE_ENV`去覆盖`NODE_ENV`，定义`process`、`process.env`会覆盖整个进程对象
- 由于插值将直接替换为文本，所以值必须包含字符串本身内部的引号。如`'"production"'`或`JSON.stringify('production')`

```javascript
new webpack.DefinePlugin({
  'process.env.NODE_ENV': '"production"'
})
// 或者
new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify('production')
})
```

```javascript
// index.js
console.log(process.env.NODE_ENV); // "production"
```

#### 功能标记

设置是否启用`生产/开发`环境的特性

```javascript
new webpack.DefinePlugin({
  'NICE_FEATURE': JSON.stringify(true),
  'EXPERIMENTAL_FEATURE': JSON.stringify(false)
});
```

#### 服务器url

在`生产/开发`构建中使用不同的服务URL

```javascript
new webpack.DefinePlugin({
  'SERVICE_URL': JSON.stringify('https://dev.example.com')
});
```

#### runtimeValue

`function (getterFunction, [string]) => getterFunction()`

监视文件的值定义变量，当监视的文件变化时将重新计算它们，即`webpack`将重新构建，它包含两个参考

- 第一个参数为一个函数，返回要分配给定义的值
- 第二个参数是要监视的文件路径数组，值为`true`时，标记模块为`unacheable`

```javascript
const fileDep = path.resolve(__dirname, 'sample.txt');

new webpack.DefinePlugin({
  BUILT_AT: webpack.DefinePlugin.runtimeValue(Date.now, [fileDep])
});
```

`BUILT_AT`的值为`sample.txt`的文件最后更新时间，如`1597953013291`

#### 参考

[webpack document DefinePlugin](https://webpack.js.org/plugins/define-plugin/)

