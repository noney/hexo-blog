---
title: webpack常用插件之CommonsChunkPlugin
date: 2020-10-13 18:24:21
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

提取`多个入口`共享的公用模块，创建一个新的`chunk`。~~CommonsChunkPlugin~~插件已经在 `webpack v4 legato`版本中删除，请使用[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)代替。将公用模块与`bundle`分离，形成单独的`chunk`，好处是，**浏览器第一次加载并缓存它，避免每次加载过大的共享代码**

<!-- more -->

##### 选项

```javascript
{
  name: string, // or
  names: string[],
  // 通用chunk的名字，如果与现在chunk重名，将通用chunk合并到重名的chunk
  // 如果是一个名字数组，相当于执行多次插件实例，且会将上次执行的结果使用chunks属性传入到下一次实例
  // If omitted and `options.async` or `options.children` is set all chunks are used, otherwise `options.filename`
  // is used as chunk name.
  // 当设置options.async，必须指定options.name为一个入口chunk，此时生成的通用chunk名字为options.async指定的字符串

  filename: string,
  // 通用chunk的名字模板，可以包含与output.filename相同的点位符
  // 如果省略，使用`output.filename` 或 `output.chunkFilename`规则
  // 不能与options.async同时使用

  minChunks: number|Infinity|function(module, count) => boolean,
  // 通用chunk最少被几个chunks引用（默认值为3）
  // 数目>=2 && <= chunks的总数
  // 值为Infinity时，表示无限大，入口文件大于等于3才生效，不会抽取公用模块，可以用来第三方库中分离自定义的公用模块
  // 值为function时，可以添加自定义逻辑（默认为块的数量）

  chunks: string[],
  // 从指定chunks中提取通用chunk
  // 如果省略从所有入口chunks中提取

  children: boolean,
  // 值为true，会提取入口chunk中异步加载（使用import()或者require.ensure()加载的模块）的子模块中的通用chunk放在父模块中
  // 此时name属性必须设置成入口chunk的name
  // 会增加初始加载时间（过大的通用chunk）
  // 不能与options.chunks同时设置，因为它们都是指定chunks源

  deepChildren: boolean,
  // 选择所有后代chunks做为源

  async: boolean|string,
  // 解决会增加初始加载时间问题，将提取的通用chunk做为一个单独的公用模块，同正常提取的公用模块一样
  // 值为boolean时，打包后的name是0，值为string时，打包后的名字就是这个string

  minSize: number,
  // 切割完要生成的chunk必须大于minSize，否则不生成新的chunk（单位为kb）
}
```

#### 提取入口文件的通用模块

提取所有入口文件的通用模块，生成一个新的`chunk`。

当我们不使用提取插件时

```javascript
// index.js
const $ = require('jquery');
const utils = require('./utils');
console.log($, utils.add(1, 2));
```

```javascript
const $ = require('jquery');
const utils = require('./utils');
console.log($, utils.add(2, 5));
```

```javascript
// utils.js
module.exports = {
  add(a, b) {
    return a + b;
  }
}
```

```javascript
// webpack.config.js
const webpack = require('webpack');
const entry = {
  index: './src/index.js',
  list: './src/list.js'
};
const output = {
  filename: '[name].js',
  path: `${__dirname}/dist`
};
const plugins = [
  new webpack.BannerPlugin('noneyli')
];
module.exports = {
  entry,
  output,
  plugins
}
```

输出结果：

```shell
   Asset    Size  Chunks                    Chunk Names
 list.js  291 kB       0  [emitted]  [big]  list
index.js  291 kB       1  [emitted]  [big]  index
   [1] ./src/utils.js 52 bytes {0} {1} [built]
   [2] ./src/index.js 97 bytes {1} [built]
   [3] ./src/list.js 97 bytes {0} [built]
```

我们可以看到`list.js、index.js`中都包含了通用模块，现在我们添加下面代码

```javascript
  new webpack.optimize.CommonsChunkPlugin({
    name: 'common',
    filename: 'common.js'
  })
```

输出结果：

```shell
    Asset       Size  Chunks                    Chunk Names
  list.js  226 bytes       0  [emitted]         list
 index.js  226 bytes       1  [emitted]         index
common.js     292 kB       2  [emitted]  [big]  common
   [1] ./src/utils.js 52 bytes {2} [built]
   [2] ./src/index.js 97 bytes {1} [built]
   [3] ./src/list.js 97 bytes {0} [built]
```

我们可以看到通用模块已经被提取到`common.js`，但是这里面包含了我们的通用逻辑代码和第三方库，这不是我们想要的，下面我们把第三方库提取出来

#### 提取第三方库

分离处三方库和业务逻辑代码

```javascript
const webpack = require('webpack');
const entry = {
  index: './src/index.js',
  list: './src/list.js',
  vender: ['jquery'] // 需要提取的库
};
const output = {
  filename: '[name].js',
  path: `${__dirname}/dist`
};
const plugins = [
  new webpack.BannerPlugin('noneyli'),
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vender',
      filename: 'vender.js',
      minChunks: Infinity // 永远无法满足的条件，避免逻辑代码提取的库
    }),
  new webpack.optimize.CommonsChunkPlugin({
    name: 'common',
    filename: 'common.js'
  })
];
module.exports = {
  entry,
  output,
  plugins
}
```

输出结果：

```javascript
    Asset       Size  Chunks                    Chunk Names
  list.js  349 bytes       0  [emitted]         list
 index.js  340 bytes       1  [emitted]         index
vender.js     288 kB       2  [emitted]  [big]  vender
common.js    3.85 kB       3  [emitted]         common
   [1] ./src/utils.js 52 bytes {0} {1} [built]
   [2] ./src/index.js 97 bytes {1} [built]
   [3] ./src/list.js 97 bytes {0} [built]
   [4] multi jquery 28 bytes {2} [built]
```

我们可以看到，第三方库、通用逻辑模块都被单独提取出来了

#### 提取通用模块到父模块

用于代码分离时，父模块通过`import()`方法异步加载的子模块，它们可能会有共同依赖，可以通过`children`属性将它们提取到父模块

```javascript
// index.js
const nav = import('./index-nav');
const loading = import('./index-loading');
console.log(nav, loading);
```

```javascript
// index-nav.js
const $ = require('jquery');
console.log($);
```

```javascript
// index-loading.js
const $ = require('jquery');
console.log($);
```

当我们不使用插件提取时

```javascript
const webpack = require('webpack');
const entry = {
  index: './src/index.js'
};
const output = {
  filename: '[name].js',
  path: `${__dirname}/dist`
};
const plugins = [
  new webpack.BannerPlugin('noneyli'),
];
module.exports = {
  entry,
  output,
  plugins
}
```

输出结果 ：

```shell
   Asset    Size  Chunks                    Chunk Names
    0.js  288 kB       0  [emitted]  [big]
    1.js  288 kB       1  [emitted]  [big]
index.js    6 kB       2  [emitted]         index
   [0] ./src/index.js 105 bytes {2} [built]
   [1] ./src/index-nav.js 45 bytes {0} [built]
   [2] ./src/index-loading.js 45 bytes {1} [built]
```

我们可以看到`0.js、1.js`就是通过`import()`调用的模块，构建完的文件中都包含了`jquery`，下面我们加上下面的代码

```javascript
  new webpack.optimize.CommonsChunkPlugin({
    children: true
  })
```

输出结果：

```shell
   Asset       Size  Chunks                    Chunk Names
    0.js  166 bytes       0  [emitted]
    1.js  166 bytes       1  [emitted]
index.js     294 kB       2  [emitted]  [big]  index
   [0] ./src/index.js 106 bytes {2} [built]
   [2] ./src/index-nav.js 45 bytes {0} [built]
   [3] ./src/index-loading.js 45 bytes {1} [built]
```

我们可以看到`jquery`已经提取到了`父模块`中（`index.js`），**如果`子模块`的通用模块过多的话，会导致`index.js`加载时间过长**

#### 提取通用模块到异步模块

与`children`功能相同，唯一区别就是不会将通用模块提取到父模块（增加加载时间），而是提取到一个单独的异步模块，当加载额外模块时（`import()引用的模块`），它将会自动`并行`下载

```javascript
  new webpack.optimize.CommonsChunkPlugin({
    name: 'index',
    async: 'vender'
  })
```

输出结果：

```shell
   Asset       Size  Chunks                    Chunk Names
    0.js     288 kB       0  [emitted]  [big]  vender
    1.js  172 bytes       1  [emitted]
    2.js  166 bytes       2  [emitted]
index.js    6.08 kB       3  [emitted]         index
   [0] ./src/index.js 105 bytes {3} [built]
   [1] ./src/index-nav.js 45 bytes {1} [built]
   [2] ./src/index-loading.js 45 bytes {2} [built]
```

我们可以看到，`0.js`就是单独的异步模块，当我们加载`1.js`或`2.js`时，它将会自动并行下载，下面构建完成的`index.js`源码，我们也能从中看出来

```javascript
// index.js
// ...
const nav = Promise.all/* import() */([__webpack_require__.e(1), __webpack_require__.e(0)]).then(__webpack_require__.bind(null, 1));
const loading = Promise.all/* import() */([__webpack_require__.e(2), __webpack_require__.e(0)]).then(__webpack_require__.bind(null, 2));
console.log(nav, loading);
// ...
```

#### minChunks值为函数

`minChunks`表示最少被多少`chunk`引用的模块才会被提取。当值为函数时，会有两个参数

1. module `object` 表示当前`chunk`及其包含的模块信息，它有两个常用属性
   - module.context 模块存储目录
   - module.resource 正在处理的模块绝对路径
2. count `number` 表示当前`chunk`及其包含的模块被引用的次数

```javascript
// src/index.js
const $ = require('jquery');
const _ = require('lodash');
console.log($, _)
```

```javascript
// src/list.js
const $ = require('jquery');
console.log($)
```

```javascript
const webpack = require('webpack');
const entry = {
  index: './src/index.js',
  list: './src/list.js'
};
const output = {
  filename: '[name].js',
  path: `${__dirname}/dist`
};
const plugins = [
  new webpack.BannerPlugin('noneyli'),
  new webpack.optimize.CommonsChunkPlugin({
    name: 'common',
    filename: '[name].js',
    minChunks: (module, count) => {
      console.log(`module.context: ${module.context}\nmodule.resource: ${module.resource}\ncount: ${count}\n`)
      return module.resource && (/node_modules/).test(module.resource) && count === 2;
    }
  })
];
module.exports = {
  entry,
  output,
  plugins
}
```

输出结果：

```shell
module.context: /Users/linoney/noney/node/webpack/src
module.resource: /Users/linoney/noney/node/webpack/src/index.js
count: 1

module.context: /Users/linoney/noney/node/webpack/node_modules/jquery/dist
module.resource: /Users/linoney/noney/node/webpack/node_modules/jquery/dist/jquery.js
count: 2

module.context: /Users/linoney/noney/node/webpack/node_modules/lodash
module.resource: /Users/linoney/noney/node/webpack/node_modules/lodash/lodash.js
count: 2

module.context: /Users/linoney/noney/node/webpack/node_modules/webpack/buildin
module.resource: /Users/linoney/noney/node/webpack/node_modules/webpack/buildin/global.js
count: 2

module.context: /Users/linoney/noney/node/webpack/node_modules/webpack/buildin
module.resource: /Users/linoney/noney/node/webpack/node_modules/webpack/buildin/module.js
count: 2

module.context: /Users/linoney/noney/node/webpack/src
module.resource: /Users/linoney/noney/node/webpack/src/list.js
count: 1

Hash: f86cc1e2929247b3b80e
Version: webpack 3.12.0
Time: 312ms
    Asset       Size  Chunks                    Chunk Names
  list.js  207 bytes       0  [emitted]         list
 index.js  207 bytes       1  [emitted]         index
common.js     836 kB       2  [emitted]  [big]  common
   [2] ./src/index.js 77 bytes {1} [built]
   [3] (webpack)/buildin/global.js 509 bytes {2} [built]
   [4] (webpack)/buildin/module.js 517 bytes {2} [built]
   [5] ./src/list.js 77 bytes {0} [built]
```

当我们需要细粒度的去提取通用模块时，这种方式会很有用。如提取通用的`npm`库

```javascript
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  minChunks: function (module) {
    // this assumes your vendor imports exist in the node_modules directory
    return module.context && module.context.includes('node_modules');
  }
});
```

#### 提取引导逻辑

将`webpack`引导逻辑提取到单独的`chunk`。使用一个没有被定义为`entry`的名字（通常使用`manifest`），详细信息请参考[缓存](https://webpack.js.org/guides/caching/)

```javascript
new webpack.optimize.CommonsChunkPlugin({
  name: 'manifest',
  minChunks: Infinity
});
```

#### 参考 

[webpack document CommonsChunkPlugin](https://webpack.js.org/plugins/commons-chunk-plugin/)
[webpack中文文档 CommonsChunkPlugin](https://www.webpackjs.com/plugins/commons-chunk-plugin/)
[webpack——CommonsChunkPlugin](https://blog.csdn.net/zy444263/article/details/85252477)
[Webpack4之SplitChunksPlugin](https://juejin.im/post/6844903603400884238)
[详解CommonsChunkPlugin的配置和用法](https://segmentfault.com/a/1190000012828879)

