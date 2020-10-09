---
title: webpack详解之resolve
date: 2020-10-09 10:27:10
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`resolve`帮助`webpack`找到模块的`绝对路径`。模块可以来自应用程序或第三方库，当代码中使用`require/import`依赖模块时，`webpack`使用 [enhanced-resolve](https://github.com/webpack/enhanced-resolve) 解析文件路径

#### 解析规则

- `绝对路径` 直接使用，不需要解析

```javascript
import "/home/me/file";
import "C:\\Users\\me\\file";
```

- `相对路径` 使用`require/import`引用依赖文件的`资源文件`所在目录做为`上下文目录`，根据相对路径，解析出绝对路径

```javascript
import "../src/file1";
import "./file2";
```

<!-- more -->

- `模块路径` 在`resolve.module`指定的目录中搜索，也可以使用`resolve.alias`指定一个别名，用来替换初始模块路径，绝对路径解析成功后

> 1. 路径指向一个文件
>
> - 文件带有扩展名，直接被打包
> - 文件不带扩展名，查找`resoluve.extensions`指定的扩展名文件，然后打包
>
> 2. 路径指向一个文件夹，通过下列步骤查找文件
>
> - 文件夹中包含`package.json`文件，顺序查找`resolve.mainFields`中指定的字段，并根据`package.json`文件中该字段确定文件路径
> - 文件夹中没有`package.json`文件，或者该文件中`main`字段没有返回有效路径，则顺序查找`resolve.mainFiles`配置中指定的文件名
> - 通过`resoluve.extensions`解析文件扩展名

***注意：***

- `loader`解析也遵循上面文件解析规则，也可以根据`resolveLoader`配置指定单独的解析规则
- 文件系统访问会被缓存，`观察者模块`只会从缓存中摘除修改过的文件；关闭观察者模式，在每次编译前都会清除缓存

#### 解析配置

使用`resolve`字段配置模块如何解析，属性值是一个`object`

- alias `object` 定义目录别名，使依赖模块变得简单

```javascript
alias: {
  Utilities: path.resolve(__dirname, 'src/utilities/'),
  Templates: path.resolve(__dirname, 'src/templates/')
}
```

```javascript
// 不使用别名
import Utility from '../../utilities/utility';
// 使用别名
import Utility from 'Utilities/utility';
```

别名末尾添加`$`，表示精确匹配

```javascript
alias: {
  xyz$: path.resolve(__dirname, 'path/to/file.js')
}
```

```javascript
import Test1 from 'xyz'; // 精确匹配，所以 path/to/file.js 被解析和导入
import Test2 from 'xyz/file.js'; // 非精确匹配，触发普通解析
```

其它情况

| 别名                             | import "xyz"                        | import "xyz/file.js"              |
| -------------------------------- | ----------------------------------- | --------------------------------- |
| {}                               | /abc/node_modules/xyz/index.js      | /abc/node_modules/xyz/file.js     |
| { xyz: "/abs/path/to/file.js" }  | /abs/path/to/file.js                | error                             |
| { xyz$: "/abs/path/to/file.js" } | /abs/path/to/file.js                | /abc/node_modules/xyz/file.js     |
| { xyz: "./dir/file.js" }         | /abc/dir/file.js                    | error                             |
| { xyz$: "./dir/file.js" }        | /abc/dir/file.js                    | /abc/node_modules/xyz/file.js     |
| { xyz: "/some/dir" }             | /some/dir/index.js                  | /some/dir/file.js                 |
| { xyz$: "/some/dir" }            | /some/dir/index.js                  | /abc/node_modules/xyz/file.js     |
| { xyz: "./dir" }                 | /abc/dir/index.js                   | /abc/dir/file.js                  |
| { xyz: "modu" }                  | /abc/node_modules/modu/index.js     | /abc/node_modules/modu/file.js    |
| { xyz$: "modu" }                 | /abc/node_modules/modu/index.js     | /abc/node_modules/xyz/file.js888  |
| { xyz: "modu/some/file.js" }     | /abc/node_modules/modu/some/file.js | error                             |
| { xyz: "modu/dir" }              | /abc/node_modules/modu/dir/index.js | /abc/node_modules/dir/file.js     |
| { xyz: "xyz/dir" }               | /abc/node_modules/xyz/dir/index.js  | /abc/node_modules/xyz/dir/file.js |
| { xyz$: "xyz/dir" }              | /abc/node_modules/xyz/dir/index.js  | /abc/node_modules/xyz/file.js     |

> ***注意：***
>
> 1. 如果在`package.json`中设置，`index.js`可以解析为其它文件
> 2. `/abc/node_modules`也可能在`/node_modules`中解析
> 3. `resolve.alias`会优化于其它模块解析
> 4. ~~[`null-loader`](https://github.com/webpack-contrib/null-loader)~~已经在`webpack 5`中弃用，使用`alias: { xyz$: false }`或`alias: {[path.resolve(__dirname, './path/to/module')]: false }`代替
> 5. `webpack 5`中支持`[string]`
> 6. 别名值为`false`，`webpack`会忽略该模块
```javascript
// [string]
alias: {
  _: [path.resolve(__dirname, 'src/utilities/'), path.resolve(__dirname, 'src/templates/')]
}
// false
alias: {
  'ignored-module': false,
  './ignored-module': false,
}
```

- aliasFields `[string]: ['browser']` 指定根据`package.json`中的哪个字段来解析，如`browser`

```javascript
resolve: {
  aliasFields: ['browser']
}
```

- cacheWidthContext `boolean` (大于等于 webpack 3.1.0) 如果启用了不安全缓存，请在缓存键(cache key)中引入 `request.context`。这个选项被 [`enhanced-resolve`](https://github.com/webpack/enhanced-resolve/) 模块考虑在内。从 webpack 3.1.0 开始，在配置了 resolve 或 resolveLoader 插件时，解析缓存(resolve caching)中的上下文(context)会被忽略。这解决了性能衰退的问题。
- descriptionFiles `[string] = ['package.json']` 用于描述的 JSON 文件
- enforceExtension `boolean = false` 是否允许模块不带扩展名，如果`true`，则必须带扩展名，如`require('./file.js')`
- ~~enforeModuleExtension~~ `boolean = false` webpack5已废弃该字段，告诉webpack是否需要为模块使用扩展
- extension `[string] = ['.wasm', '.mjs', '.js', '.json']`，当指定依赖模块不带扩展名时 ，使用该字段值顺序进行匹配

```javascript
resolve: {
  extension: ['.wasm', '.mjs', '.js', '.json']
}
```

```javascript
// 在../path/to/目录下，依次查找file.wasm、file.mjs、file.js、file.json，直到找到
import File from '../path/to/file';
```

***注意：***配置该字段，将覆盖默认值

- mainFileds `[string]` （***依赖为目录时使用***）当我们从`npm package`导入依赖时，`mainFileds`字段决定使用`package.json`包中哪个字段来查找该依赖，如果我们不指定，它的默认值由`webpack target`字段决定

```javascript
// target值为webworker、web或者不指定时
resolve: {
  mainFields: ['browser', 'module', 'main']
}
// target值其它（包括node）
resolve: {
  mainFields: ['module', 'main']
}
```

此时我们有下面的例子

```javascript
// index.js
import * as D3 from "d3"
```

```json
// package.json
{
  ...
  "main": "build/d3.Node.js",
  "browser": "build/d3.js",
  "module": "index",
  ...
}
```

这里我们会使用`browser`解析文件，因为它是`mainFileds`中的第一项，如果是使用`webpack`打包的`node`应用程序，会使用`module`字段

- mainFiles `[string] = ['index']` （***依赖为目录时使用***）当解析文件路径中不包含文件名时，按照顺序查询指定文件名

```javascript
resolve: {
  mainFiles: ['index', 'main']
}
```

```javascript
// 依次查找`/abc/path/index`、`/abc/path/main`
import "/abc/path/"
```

- module `[string] = ['node_modules']` 指定 `webpack`解析模块时搜索的目录，`相对路径`和`绝对路径`都适用

> 1. 相对路径，搜索当前目录及祖先目录即 `./node_modules`, `../node_modules` 等等），类似于`node`模块查询方式
> 2. 绝对路径，在给定目录中搜索

```javascript
// 指定src目录也为目标目录，它将优先于node_modules
resolve: {
  module: [path.resolve(__dirname, "src"), 'node_modules']
}
```

- unsafeCache `RegExp` `[RegExp]` `boolean: true` 启用主动，但不安全的模块缓存，`true`将缓存所有

```javascript
resolve: {
  unsafeCache: /src\/utilities/
}
```

- plugins `[plugins]` 解析插件列表，如[`DirectoryNamedWebpackPlugin`](https://www.npmjs.com/package/directory-named-webpack-plugin)

```javascript
resolve: {
  plugins: [
    new DirectoryNamedWebpackPlugin()
  ]
}
```

- preferRelative `boolean` 启动此项时，`webpack`会将`模块路径`解析当作`相对路径`方式解析

```javascript
resoluve: {
  preferRelative: true
}
```

```javascript
// src/index.js
// let's say `src/logo.svg` exists
import logo1 from 'logo.svg'; // this is viable when `preferRelative` enabled
import logo2 from './logo.svg'; // otherwise you can only use relative path to resolve logo.svg

// `preferRelative` is enabled by default for `new URL()` case
const b = new URL('module/path', import.meta.url);
const a = new URL('./module/path', import.meta.url);
```

- symlinks `boolean = true` 解析软链时，`true`解析为真实路径；`false`解析为链接位置

```javascript
resolve: {
  symlinks: true
}
```

***注意：***当使用符号链接包(如npm link)的工具时，这可能会导致模块解析失败。

- restrictions `[string, RegExp]` 设置哪些文件不会被解析

```javascript
resolve: {
  restrictions: [/\.(sass|scss|css)$/]
}
```

- roots `[string]` 设定相对于服务的`url`（以`/`开头）的依赖路径的上下文列表，默认为`context`属性值， 在非`window`操作系统上，会首先做为`绝对路径`解析

```javascript
const fixtures = path.resolve(__dirname, 'fixtures');
module.exports = {
  //...
  resolve: {
    roots: [__dirname, fixtures]
  }
};
```

- importsFields `[string]` 设定以`#`开头的依赖路径，使用`package.json`中的哪个字段进行匹配

```javascript
resolve: {
  importFields: ['browser', 'module', 'main']
}
```

- fallback `boolean` 指定解析失败时，备用的路径

```javascript
resolve: {
  fallback: {
    xyz: path.resolve(__dirname, 'path/to/file.js')
  }
}
```

#### loader解析

`resolveLoader`配置`loader`的解析规则（默认使用文件的解析规则）

`object { modules [string] = ['node_modules'], extensions [string] = ['.js', '.json'], mainFields [string] = ['loader', 'main']}`

```javascript
module.exports = {
  //...
  resolveLoader: {
  modules: ['node_modules'],
  extensions: ['.js', '.json'],
  mainFields: ['loader', 'main']
  }
};
```

- ~~moduleExtensions~~ `[string]` `webpack 5`中已废弃，解析加载器使用的扩展名/后缀，***强烈建议使用全名***

```javascript
module.exports = {
  //...
  resolveLoader: {
    moduleExtensions: ['-loader']
  }
};
```

#### 参考

[webpack document Resolve](https://webpack.js.org/configuration/resolve/)
[webpack中文文档 Resolve](https://www.webpackjs.com/configuration/resolve/)