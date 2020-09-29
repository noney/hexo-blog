---
title: webpack详解之mode和content
date: 2020-09-29 14:28:10
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

介绍`mode`和`content`两个配置项

#### mode

`string = 'production': 'none' | 'development' | 'production'`

`mode`用来配置`webpack`在什么模式下运行，`webpack`会执行相应的内置优化（默认值为`production`）

- 在配置文件里配置

```javascript
mode: 'development'
```

- 在`cli`里指定

```shell
webpack --mode=development
```

<!-- more -->

| 值          | 描述                                                         |      |
| ----------- | ------------------------------------------------------------ | ---- |
| development | 设置`DefinePlugin['process.env.NODE_ENV']`的值为`development`。启用`NamedChunksPlugin` and `NamedModulesPlugin`插件 |      |
| production  | 设置`DefinePlugin['process.env.NODE_ENV']`的值为`production`。启用`FlagDependencyUsagePlugin` , `FlagIncludedChunksPlugin` , `ModuleConcatenationPlugin` , `NoEmitOnErrorsPlugin` , `OccurrenceOrderPlugin` , `SideEffectsFlagPlugin` and `TerserPlugin`插件 |      |
| none        | 不内置任何优化                                               |      |

***注意：***设置`NODE_ENV`环境变量，并不会设置`mode`

如果想根据参考去分辨`mode`，则必须导出一个函数

```javascript
var config = {
  entry: './app.js'
  //...
};
module.exports = (env, argv) => {
  if (argv.mode === 'development') {
    config.devtool = 'source-map';
  }
  if (argv.mode === 'production') {
    //...
  }
  return config;
};
```

#### context

`context`用于指定`入口`和`loader`的上下文，是一个绝对路径，默认是`当前目录`

```javascript
{
  context: `${__dirname}/src`
}
```

#### 参考

[Entry and Context](https://webpack.js.org/configuration/entry-context/#context)
[Mode](https://webpack.js.org/configuration/mode/)
[显微镜下的webpack4的新特性：mode详解](https://www.cnblogs.com/cherryvenus/p/9808320.html)

