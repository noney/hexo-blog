---
title: webpack详解之output
date: 2020-09-27 14:40:08
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`output`用来配置`webpack`出口，即构建结果输出的位置

#### auxiliaryComment 

`string` `object`

当和`library、libraryTarget`同时使用时，为导出代码`插入注释`

- 值为一个`字符串`，为`libraryTarget`每种类型插入`相同`注释

```javascript
// webpack.config.js
output: {
  library: "someLibName",
  libraryTarget: "umd",
  filename: "someLibName.js",
  auxiliaryComment: "Test Comment"
}
```

<!-- more -->

```javascript
// someLibName.js
(function webpackUniversalModuleDefinition(root, factory) {
  // Test Comment
  if(typeof exports === 'object' && typeof module === 'object')
  module.exports = factory(require('lodash'));
  // Test Comment
  else if(typeof define === 'function' && define.amd)
  define(['lodash'], factory);
  // Test Comment
  else if(typeof exports === 'object')
  exports['someLibName'] = factory(require('lodash'));
  // Test Comment
  else
  root['someLibName'] = factory(root['_']);
})(this, function(__WEBPACK_EXTERNAL_MODULE_1__) {
  // ...
});
```

- 值为一个对象，为为`libraryTarget`每种类型插入`不同`注释

```javascript
auxiliaryComment: {
  root: "Root Comment",
  commonjs: "CommonJS Comment",
  commonjs2: "CommonJS2 Comment",
  amd: "AMD Comment"
}
```

#### charset 

`boolean = true` 

是否插入`charset="utf-8"`在页面`script`标签内

```javascript
output: {
  charset: true
}
```

***注意：***虽然`scripts`标签的~~charset~~属性已经弃用，但为了兼容老版浏览器，`webpack`默认添加它

#### chunkFilename

`string = '[id].js'`

设置非入口（如懒加载的模块）`chunk`文件的名称。默认使用`[id].js`，或者使用`filename`中推断出的值（`[name]`会被替换为`[id]`或`[id].`前缀）[参考](https://www.cnblogs.com/wuxianqiang/p/11183374.html)

```javascript
output: {
  chunkFilename: '[name].js'
}
```

#### chunkLoadTimeout

`number = 120000`

设置`chunk`请求超时时间。从`2.6.0`开始支持

```javascript
output: {
  chunkLoadTimeout: 3000
}
```

#### chunkCallbackName

`string = 'webpackChunkwebpack'`

设置`chunk`加载完成回调函数

```javascript
output: {
  chunkCallbackName: "chunkCallBack"
}
```

#### crossOriginLoading

`boolean = false`  `string: 'anonymous' | 'use-credentials'`

使用`JSONP`的方式按需加载跨域`chunk`，只有当`target`属性为`web`时有效

- anonymous `不带凭证`启用跨域加载
- use-credentials `带凭证`使用跨域加载

#### devtoolFallbackModuleFilenameTemplate

`string` `function (info)`

#### devtoolModuleFilenameTemplate

`string = 'webpack://[namespace]/[resource-path]?[loaders]'` `function (info) => string`

#### devtoolNamespace

`string`

#### filename

`string` `function (pathData, assetInfo) => string`

设置输出文件名

单入口输出

```javascript
output: {
  filename: 'bundle.js'
}
```

多入口输出

- 使用入口文件名

```javascript
output: {
  filename: '[name].bundle.js'
}
```

- 使用内部`chunk id`

```javascript
output: {
  filename: '[id].bundle.js'
}
```

- 每次构建生成的唯一`hash`

```javascript
output: {
  filename: '[name].[hash].bundle.js'
}
```

- 基于每个`chunk`内容生成的`hash`

```javascript
output: {
  filename: '[chunkhash].bundle.js'
}
```

- 基于单个`chunk`内容生成的`hash`

```javascript
output: {
  filename: '[contenthash].bundle.js'
}
```

- 使用`function`生成

```javascript
output: function(pathData) {
  return pathData.chunk.name === 'main' ? '[name].js': '[name]/[name].js';
}
```


#### hash、chunkhash、contenthash区别

- hash 项目级别`hash`，所有文件都使用这个`hash`。只要有一个文件改变，项目的`hash`就会改变
- chunkhash 模块级别`hash` ，模块内引用的文件都全用这个`hash`。当一个模块引入许多小模块时，如果一个小模块改变，那整个模块的`hash`就会改变
- contenthash 文件级别`hash`，每个文件有自己的`hash`，只有文件内容改变时，当前文件的`hash`才会改变

[参考](https://www.jianshu.com/p/e687aa625f71)

***总结：***可以在`filename`模块中使用的占位符

| 值            | 描述                              |      |
| ------------- | --------------------------------- | ---- |
| [hash]        | 模块标识                          |      |
| [contenthash] | 单个文件内容生成的标识            |      |
| [chunkhash]   | `chunk`内容生成的标识             |      |
| [name]        | 模块名称                          |      |
| [id]          | 模块标识                          |      |
| [query]       | 模块查询                          |      |
| [function]    | 函数，可以返回`filename [string]` |      |

`[hash]、[chunkhash]`的长度可以使用`[hash:16]`方式设置（默认长度是20），还可以使用`hashDigestLength`属性配置`全局`长度

#### assetModuleFilename

`string = '[hash][ext][query]'`

静态文件的`filename`

#### globalObject

`string = 'window'`

当我们写一个库时，且当`libraryTarget`为`umd`（通用模块规范）时，此项将设置全局对象的指向

```javascript
  output: {
  library: 'myLib',
  libraryTarget: 'umd',
  filename: 'myLib.js',
  globalObject: 'this'
  }
```

#### uniqueName

`string`

`webpack`构建的堆一名字，避免多个`webpack`运行冲突。默认名字为`library`设置的值或者`package.json`中设置的包名，如果这两个都没有设置，它的值为`''`

`uniqueName`的值将被用于

- [`output.jsonpFunction`](https://webpack.js.org/configuration/output/#outputjsonpfunction)
- [`output.chunkCallbackName`](https://webpack.js.org/configuration/output/#outputchunkcallbackname)

```javascript
output: {
  uniqueName: 'my-package-xyz'
}
```

#### hashDigest

`string = 'hex'`

生成`hash`使用的编码

#### hashDigestLength

`number = 20`

`hash`长度

#### hashFunction

`string = 'md4'` `function`

散列算法

#### hashSalt

通过`Nodejs`的`hash.update`方法来更新`hash`

#### hotUpdateChunkFilename

`string = '[id].[hash].hot-update.js'`

自定义热更新`chunk`文件名，只允许使用`[id]、[hash]`占位符

```javascript
output: {
  hotUpdateChunkFilename: '[id].[hash].hot-update.js'
}
```

***注意：***通常不需要更改

#### hotUpdateFunction

`string`

只有当`target`属性设置为`web`时有效。使用`jsonp`方式加载热更新

#### hotUpdateMainFilename

`string = '[hash].hot-update.json'` `function`

自定义`main`文件名，只允许使用`[hash]`占位符

***注意：***通常不需要更改

#### jsonpFunction

`string = 'webpackJsonp'`

只有当`target`属性设置为`web`时有效。使用`jsonp`方式按需加载`chunk`

如果设置了`library`选项，`jsonpFunction`将使用该选项的值

默认按需加载的`chunk`使用

```javascript
// example-on-demand-chunk.js
(window.webpackJsonp = window.webpackJsonp || []).push(/* ... */);
```

它使用了一个默认值，多个`webpack`运行中，为了避免冲突我们要设置各自不同的值

```javascript
output: {
  jsonpFunction: 'wpJsonpFlightsWidget'
}
```

现在按需加载的`chunk`使用

```javascript
// example-on-demand-chunk.js
(window.wpJsonpFlightsWidget = window.wpJsonpFlightsWidget || []).push(/* ... */);
```

#### library

`string` `object`

当我们编写一个库时，使用`librayr`设置对外暴露变量的名字，访问方式取决于`libraryTarget`属性的值

```javascript
output: {
  library: "myLibrary"
}
```

***注意：***

- 如果`entry`值为数组，那么只会暴露数组中的最后一个模块
- 从 `webpack 3.1.0` 开始；当 `libraryTarget: "umd"`，`library`值可以设置为一个`object`

#### scriptType

`string: 'module' | 'text/javascript'` `boolean = false`

自定义`scripts`标签`type`属性值。当设置`module`属性值为`true`时

```javascript
output: {
  scriptType: 'module'
}
```

#### libraryExport

`string` `[string]`

当我们编写一个库时，使用`librayExport`来设置对外暴露哪些接口。它的默认为`undefined`，等同于将`libraryTarget`设置为`''`，表示将导出整个对象，下面的例子演示了这个配置，所有例子都使用`libraryTarget: 'var'`

- `libraryExport: 'default'` 将`default`模块暴露出来

```javascript
var MyDefaultModule = _entry_return_.default;
//调用
MyDefaultModule.doSomething();
```

- `libraryExport: 'myModule'` 将`指定模块`暴露出来

```javascript
var MyModule = _entry_return_.MyModule;
//调用
MyModule.doSomething();
```

- `libraryExport: ['MyModule', 'MySubModule']` 数组将被解析为一个模块路径，将`指定模块`暴露出来

```javascript
var MySubModule = _entry_return_.MyModule.MySubModule;
MySubModule.doSomething();
```

#### libraryTarget

`string = 'var'`

当我们编写一个库时，使用`librayTarget`来设置以什么形式向外暴露它

**做为一个`变量`暴露，变量名为`library`属性值**

- `libraryTarget: var`（默认值）

```javascript
var MyLibrary = _entry_return_;

// In a separate script...
MyLibrary.doSomething();
```

***注意：***`library`值如果为空，会无法赋值

- `libraryTarget: assign`以变量形式暴露（不会使用`var`定义），产生一个隐含的全局变量

```javascript
MyLibrary = _entry_return_;
```

***注意：***`library`值如果为空，将产生一个破损的输出 `bundle`

**暴露给对象，作为它的一个属性，属性名为`library`属性值**

- `libraryTarget: this`

```javascript
this["MyLibrary"] = _entry_return_;

// 在一个单独的 script……
this.MyLibrary.doSomething();
MyLibrary.doSomething(); // 如果 this 是 window
```

- `libraryTarget: window`

```javascript
window["MyLibrary"] = _entry_return_;

window.MyLibrary.doSomething();
```

- `libraryTarget: global`

```javascript
global["MyLibrary"] = _entry_return_;

global.MyLibrary.doSomething();
```

- `libraryTarget: commonjs`

```javascript
exports["MyLibrary"] = _entry_return_;

require("MyLibrary").doSomething();
```

***注意：***如果`library`值为空，返回的所有属性将直接赋值给对象，不会去检查是否已存在该属性，即没有了`MyLibrary`这个命名空间

**以`模块系统`方式暴露，将生成一个带有完事头的包，用来与各个模块系统兼容**

- `libraryTarget: 'commonjs2'` 允许以`CommonJS`形式调用

```javascript
module.exports = _entry_return_;

require('MyLibrary').doSomething();
```

***注意：***直接暴露给了`module.exports`，没有用到`library`属性值，所以`library`可以为空

- `libraryTarget: 'amd'` 允许以`AMD`形式调用

```javascript
output: {
  library: 'MyLibrary',
  libraryTarget: 'amd'
}
```

```javascript
define('MyLibrary', [], function() {
  return _entry_return_;
});
```

```javascript
require(['MyLibrary'], function(MyLibrary) {
  // Do something with the library...
});
```

***注意：***如果省略`library`属性，将输出

```javascript
define([], function() {
  return _entry_return_;
});
```

- `libraryTarget: umd` 允许以`CommonJS、AMD`和`全局变量`形式调用

```javascript
output: {
  library: 'MyLibrary',
  libraryTarget: 'amd'
}
```

```javascript
(function webpackUniversalModuleDefinition(root, factory) {
  if(typeof exports === 'object' && typeof module === 'object')
  module.exports = factory();
  else if(typeof define === 'function' && define.amd)
  define([], factory);
  else if(typeof exports === 'object')
  exports['MyLibrary'] = factory();
  else
  root['MyLibrary'] = factory();
})(typeof self !== 'undefined' ? self : this, function() {
  return _entry_return_;
});
```

如果省略`library`属性，所有属性将赋值给根对象

```javascript
(function webpackUniversalModuleDefinition(root, factory) {
  if(typeof exports === 'object' && typeof module === 'object')
  module.exports = factory();
  else if(typeof define === 'function' && define.amd)
  define([], factory);
  else {
  var a = factory();
  for(var i in a) (typeof exports === 'object' ? exports : root)[i] = a[i];
  }
})(typeof self !== 'undefined' ? self : this, function() {
  return _entry_return_;
});
```

从3.1.0开始，可以为每个目标指定一个名字

```javascript
output: {
  library: {
    root: 'MyLibrary',
    amd: 'my-library',
    commonjs: 'my-common-library'
  },
  libraryTarget: 'umd'
}
```

- `libraryTarget: 'system'`暴露给 [`System.register`](https://github.com/systemjs/systemjs/blob/master/docs/system-register.md)从`4.30.0`开始支持

```javascript
output: {
  libraryTarget: 'system'
}
```

```javascript
System.register([], function(_export) {
  return {
  setters: [],
  execute: function() {
    // ...
  },
  };
});
```

```javascript
output: {
  library: 'my-library',
  libraryTarget: 'system'
}
```

```javascript
System.register('my-library', [], function(_export) {
  return {
  setters: [],
  execute: function() {
    // ...
  },
  };
});
```

```javascript
// Log the URL of the current SystemJS module
console.log(__system_context__.meta.url);

// Import a SystemJS module, with the current SystemJS module's url as the parentUrl
__system_context__.import('./other-file.js').then(m => {
  console.log(m);
});
```

**以其它方式暴露**

`libraryTarget: 'jsonp'`返回值包装到`jsonp`

```javascript
MyLibrary(_entry_return_);
```

库的依赖关系将由`externals`配置定义。

#### importFunctionName

`string = 'import'`

填充`import()`引入模块的名字

```javascript
output: {
  importFunctionName: '__import__'
}
```

#### path

`string = path.join(process.cwd(), 'dist')`

构建完成输出的`绝对`路径

```javascript
output: {
  path: path.resolve(__dirname, 'dist/assets')
}
```

#### pathinfo

`boolean`

`bundle`中包含模块的所有注释。`开发环境`默认为`true`，`生产环境`默认为`false`

***注意：***这个注释还包括`tree shaking`的信息

#### publicPath

`string = ''` `function`

设置`按需加载`或加载`外部资源（图片，文件等）`的公共路径

- 相对`URL` 相对当前`html`页面或 （`<base>` 标签）解析

```javascript
publicPath: "assets/", // 相对于 HTML 页面
publicPath: "../assets/", // 相对于 HTML 页面
publicPath: "", // 相对于 HTML 页面（目录相同）
```

- 相对于服务的`URL`

```javascript
output: {
  publicPath: "/assets/", // 相对于服务(server-relative)
}
```

```html
<link href="/assets/spinner.gif" />
```

```css
background-image: url(/assets/spinner.gif);
```

- 相对于协议的 URL

```javascript
output: {
  publicPath: "//cdn.example.com/assets/", // CDN (协议相同)
}
```

- 绝对 URL

```javascript
output: {
  publicPath: "https://cdn.example.com/assets/", // CDN（总是 HTTPS 协议）
}
```

***注意：***

- 该属性的值将作为载入文件的前缀，所以大多数情况都是以`/`结束
- `webpack-dev-server`也会根据它来确定从哪里提供输出文件

#### sourceMapFilename

`string = '[file].map[query]'`

生成的`map`文件名，只有在`devtool`中设置为`source-map`有效。可以使用`[name]、[id]、[hash]、[chunkhash]、[file]`占位符，建议只使用`[file]`占位符，因为其他占位符在非 chunk 文件(non-chunk files)生成的 SourceMap 时不起作用

- `[file]` 替换为原始文件的文件名
- `[filebase]` 替换为文件的`basename`（文件名带扩展名）

#### sourcePrefix

`string = ''`

设置输出`bundle`的每行的`前缀`

```javascript
output: {
  sourcePreFix: '\t'
}
```

***注意：***虽然可以使代码看起来漂亮，但会导致多行字符串的问题；通常不需要设置它

#### strictModuleExceptionHandling

`boolean = false`

设置`webpack`对模块异常的处理。

- false `require`模块抛出异常时，`webpack`不会从`require.cache`缓存中删除它

```javascript
//test.js
throw new Error('error');
```

```javascript
require('./test') // 抛出异常
require('./test') // 不抛出异常
```

- true `require`模块抛出异常时，`webpack`会从`require.cache`缓存中删除它

```javascript
//test.js
throw new Error('error');
```

```javascript
require('./test') // 抛出异常
require('./test') // 抛出异常
```

***注意：***出于性能的考虑，默认值为`false`。即不从缓存中删除模块，所以只会在第一次调用时`抛出异常`（会导致与 node.js 不兼容）

#### umdNamedDefine

`boolean`

当`libraryTarget: umd`时，设置`umdNamedDefine`为`true`，将给`amd`模块命名，否则使用匿名定义

```javascript
output: {
  library: 'MyLibrary',
  libraryTarget: 'umd',
  umdNamedDefine: true
}
```

```javascript
(function webpackUniversalModuleDefinition(root, factory) {
  if(typeof exports === 'object' && typeof module === 'object')
  module.exports = factory();
  else if(typeof define === 'function' && define.amd)
  define('MyLibrary', [], factory);
  else if(typeof exports === 'object')
  exports['MyLibrary'] = factory();
  else
  root['MyLibrary'] = factory();
})(typeof self !== 'undefined' ? self : this, function() {
  return _entry_return_;
});
```

#### enabledLibraryTypes

`[string]`

启用库的类型列表

```javascript
output: {
  enabledLibraryTypes: ['module']
}
```

#### ~~futureEmitAssets~~

`boolean = false`

将在`webpack v5.0.0`中被移除，将做为默认设置

```javascript
output: {
  futureEmitAssets: true
}
```

#### ecmaVersion

`number = 6`

设置`webpack`生成的代码最大`ecma`版本

- \>=5，<=11
- \>=2009，<=2020

```javascript
output: {
  ecmaVersion: 6
}
```

***注意：***`webpack 4`中`ecmaVersion`默认值为`5`

#### compareBeforeEmit

`boolean = true`

设置`webpack`是否向磁盘重复写入`已存在`的且`内容相同`的文件

```javascript
output: {
  compareBeforeEmit: true
}
```

#### wasmLoading

`boolean = false` `string`

设置加载`WebAssembly`模块的方法，默认包含的方法有`fetch (web/WebWorker)`、`async-node（Node.js）`，其它的方法可以通过插件添加

- 如果`target`属性值为`'web'`, `'webworker'`, `'electron-renderer'` or `'node-webkit'`默认包含方法`fetch`
- 如果`target`属性值为`'node'`, `'async-node'`, `'electron-main'` or `'electron-preload'`默认包含方法`async-node`

```javascript
output: {
  wasmLoading: 'fetch'
}
```

#### enabledWasmLoadingTypes

`[string]`

启用`wasm loading`的类型列表

```javascript
output: {
  enabledWasmLoadingTypes: ['fetch']
}
```

#### iife

`boolean = true`

设置`webpack`在构建的代码内添加`iife`

```javascript
output: {
  iife: true
}
```

#### module

`boolean = true`

设置模块作为一个库输出。

```javascript
output: {
  module: true
}
```

当`module`为`true`时，会设置下列属性

```javascript
{
  output: {
    iife: false,
    libraryTarget: 'module',
    scriptType: 'module',
  }
  terserOption: {
    module: true
  }
}
```

#### #### 参考

[webpack document output](https://webpack.js.org/configuration/output/)
[webpack中文文档 output](https://www.webpackjs.com/configuration/output/)
[什么是前端模块规范AMD，CMD，CommonJS和UMD？](https://www.jianshu.com/p/00ee4e45c0cd)



