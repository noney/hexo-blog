---
title: webpack常用插件之DllPlugin
date: 2020-10-19 17:32:13
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

分割`bundle`，提高构建性能，和`DllReferencePlugin`配合使用

#### DllPlugin

使用单独的配置构建一个`dll`包。它生成一个`manifest.json`，`DllReferencePlugin`使用该文件映射依赖关系

#### 选项

- context `可选` `manifest`文件中包含模块的解析上下文（默认值为`webpack`的上下文）
- format `boolean=false`，值为`true`时，生成的`manifest.json`文件会被格式化
- name `dll`暴露的对象名（`manifest.json`中的`name`字段），可以使用[点位符](https://github.com/webpack/webpack/blob/master/lib/TemplatedPathPlugin.j): `[fullhash]` & `[name]`
- path `manifest json`文件的输出路径（`绝对路径`，必须指定文件名）
- entryOnly `boolean=true`，值为`true`时，只有入口文件才会被导出
- type `dll bundle`类型

<!-- more -->

***注意：***

- 我们建议只在`entryOnly: true`的情况下使用`DllPlugin`，否则在`DLL`中`tree shaking`将无法工作，因为所有的导出都可能被使用

- 将此插件与`output.library`结合使用，将暴露（也就是放入全局作用域）`dll`函数

#### DllReferencePlugin

在主`webpack`配置中使用，引用`manifest.json`文件，预先构建依赖关系

#### 选项

- context `manifest`文件中包含模块的解析上下文（`绝对路径`）
- extensions 解析`dll`包中的模块使用的扩展名（仅在`scope`使用时有效）
- manifest `object` `dll`模块清单（`manifest.json`文件）
- content `可选` 请求到模块`id`的映射（默认为`manifest.content`）
- name  `可选` `dll`暴露的一个标识符（默认为`manifest.name`）
- scope  `可选` 用于访问`dll`内容的前缀
- sourceType `可选` 设置`dll`如何导出

#### 模式

这个插件可以在`作用域`或`映射`模式下使用

- 作用域 当我们本置了`scope`，如`scope = 'xyz'`，我们就可以通过`require('xyz/abc')`访问`abc`模块
- 映射 如果我们引用的文件与`dll`中的文件映射成功，则使用`dll`文件，此时不会将依赖模块构建到主包中

#### 使用

`DllReferencePlugin` 和 `DllPlugin` 在不同的文件中配置

```javascript
// webpack.vendor.config.js
const path = require('path');

new webpack.DllPlugin({
  context: __dirname,
  name: '[name]_[fullhash]',
  path: path.join(__dirname, 'manifest.json'),
})
```

```javascript
// webpack.app.config.js
new webpack.DllReferencePlugin({
  context: __dirname,
  manifest: require('./manifest.json'),
  scope: 'xyz',
  sourceType: 'commonjs2'
});
```

#### 注意

`webpack 4`后构建性能大大提高，`dll`方式已经没有太大的应用场景，请参考[你真的需要 Webpack DllPlugin 吗？](https://www.cnblogs.com/skychx/p/webpack-dllplugin.html)

#### 参考

[webpack document DllPlugin](https://webpack.js.org/plugins/dll-plugin/)

