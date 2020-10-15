---
title: webpack常用插件之ContextReplacementPlugin
date: 2020-10-15 15:28:25
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

在上下文中，当`webpack`遇到使用表达式方式引用模块（如`require('./src/json/' + name + '.json')`）时，编译阶段它拿不到变量`name`的值，所以它只能使用规则（如`^\.\/.?\.json$`）在目录`./src/json/`中去推断匹配的模块，它会把所有匹配到的模块构建到最终的`bundle`文件中

```javascript
// src/index.js
let name = 1;
let one = require(`./json/${name}.json`);
```

```json
// src/json/1.json
{
  "one": 1
}
```

```json
// src/json/2.json
{
  "two": 2
}
```

<!-- more -->

```javascript
// webpack.config.js
const webpack = require('webpack');
const CompressionPlugin = require("compression-webpack-plugin");

const entry = {
  index: './src/index.js',
  list: './src/list.js',
  vender: ['jquery', 'lodash']
};
const output = {
  filename: '[name].js',
  path: `${__dirname}/dist`
};
const plugins = [
  new webpack.BannerPlugin('noneyli')
];
const stats = {
  colors: true,
  reasons: true
};
module.exports = {
  stats,
  mode: 'none',
  entry,
  output,
  plugins
}
```

输出结果（输出入长，截取部分）：

```shell
  ./src/json/1.json 19 bytes [optional] [built] [code generated]
    context element ./1.json ./src/json/ sync ^\.\/.?\.json$ ./1.json
  ./src/json/2.json 19 bytes [optional] [built] [code generated]
    context element ./2.json ./src/json/ sync ^\.\/.?\.json$ ./2.json
./src/json/ sync ^\.\/.?\.json$ 186 bytes [built] [code generated]
```

我们可以看到，`1.json、2.json`都被引入到`index.js`文件了，但是我们在`index.js`里面只是引用了`1.json`，这样会导致构建包变大，`ContextReplacementPlugin`插件就是为了解决这个问题

#### 使用

```javascript
new webpack.ContextReplacementPlugin(
  // 去匹配文件或目录，如上例中的`./src/json/`
  resourceRegExp: RegExp,
  // 替换默认资源，如上例中的 `^\.\/.?\.json$`，如果为相对路径，会相对于前一匹配资源路径去解析
  newContentResource?: string | RegExp,
  // 是否递归查找
  newContentRecursive?: boolean,
  // 匹配`newContentResource`新上下文件中的文件
  newContentRegExp?: RegExp
)
```

我们在上面例子的`webpack.config.js`文件中，加入`ContextReplacementPlugin`插件

```javascript
new webpack.ContextReplacementPlugin(/src[\/\\]json/, /^\.\/1.json$/, true)
// 或者
new webpack.ContextReplacementPlugin(/src[\/\\]json/, '', true, /^\.\/1.json$/)
```

输出结果（输出入长，截取部分）：

```shell
./src/json/1.json 19 bytes [optional] [built] [code generated]
context element ./1.json ./src/json/ sync ^\.\/1.json$ ./1.json
```

我们可以看到，只有`1.json`被引入到了`index.js`文件

#### 内容回调

```javascript
new webpack.ContextReplacementPlugin(
  resourceRegExp: RegExp,
  newContentCallback: (data) => void
);
```

`newContentCallback`的参数是一个对象，我们可以通过覆盖对象的`resource、regExp`的属性来替换默认规则

```javascript
{
  addon: '',
  resource: '/Users/linoney/noney/node/webpack/src/json',
  resolveDependencies: [Function: bound resolveDependencies],
  context: '/Users/linoney/noney/node/webpack/src',
  dependencies: [
  CommonJsRequireContextDependency {
    weak: false,
    optional: false,
    loc: [SourceLocation],
    options: [Object],
    userRequest: './json',
    critical: false,
    hadGlobalOrStickyRegExp: false,
    request: undefined,
    range: [Array],
    valueRange: [Array],
    replaces: [Array]
  }
  ],
  resolveOptions: {},
  fileDependencies: Set {
  '/Users/linoney/noney/node/webpack/package.json',
  '/Users/linoney/noney/node/webpack/src/json'
  },
  missingDependencies: Set { '/Users/linoney/noney/node/webpack/src/package.json' },
  contextDependencies: Set {},
  request: './json',
  recursive: true,
  regExp: /^\.\/.*\.json$/,
  mode: 'sync',
  category: 'commonjs'
}
```

我们修改`regExp`属性

```javascript
  new webpack.ContextReplacementPlugin(/src[\/\\]json/, (context) => {
    Object.assign(context, {
      regExp: /^\.\/1\.json/
    });
  })
```

同样可以得到上面的结果：

```shell
    ./src/json/1.json 19 bytes [optional] [built] [code generated]
    context element ./1.json ./src/json/ sync ^\.\/1\.json ./1.json
```

#### 其它选项

```javascript
new webpack.ContextReplacementPlugin(
  resourceRegExp: RegExp,
  newContentResource: string,
  newContentCreateContextMap: object // mapping runtime-request (userRequest) to compile-time-request (request)
);
```

`newContentResource` 和 `newContentCreateContextMap` 可以一起使用，以更有针对性的方式重定向请求。newContentCreateContextMap允许你映射运行时请求，以对象的形式编译请求

```javascript
new ContextReplacementPlugin(/selector/, './folder', {
  './request': './request',
  './other-request': './new-request'
});
```

#### 参考

[webpack document ContextReplacementPlugin](https://webpack.js.org/plugins/context-replacement-plugin/)
[ContextReplacementPlugin](https://cloud.tencent.com/developer/section/1477555)

