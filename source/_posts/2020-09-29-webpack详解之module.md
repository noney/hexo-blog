---
title: webpack详解之module
date: 2020-09-29 15:00:41
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`module`用来处理项目中不同类型的模块

#### noParse

`RegExp` `[RegExp]` `function(resource)` `string` `[string]`

`noParse`配置需要被`忽略`的文件，可以使用它来忽略大型库（如`jquery`）来提高构建性能

```javascript
module: {
  noParse: /jquery|lodash/
}
```

```javascript
module: {
  noParse: (content) => /jquery|lodash/.test(content)
}
```

<!-- more -->

#### unsafeCache

`boolean` `function (module)`

缓存模块解析路径 [参考](https://blog.csdn.net/qq_29438877/article/details/105259808)

- false 如果`cache`禁用
- true 如果`cache`启用并且模块来在`node`模块

```javascript
module: {
  unsafeCache: false
}
```

***注意：***`webpack 4`中，`unsafeCache`默认为`true`

#### rules

`[Rule]`

`rules`是一个数组，匹配不同模块，使用相应`loader`解析

> **Rule**
>
> `object`
>
> 一个规则对象包括`条件`、`结果`、`嵌套规则`
>
> ***条件***
>
> 1. resource 资源文件的绝对地址，已根据 [resolve](https://www.webpackjs.com/configuration/resolve)规则解析。属性`test、include、exclude、resource`对它匹配
> 2. issuer 发起请求资源文件的绝对地址。是导入时的位置。属性`issuer`对它匹配
>
> 比如，我们在`app.js`中引入`./style.css`。例子中`resource`是`/path/to/style.css`；`issuer`是`/path/to/app.js`
>
> ***结果***
>
> 1. loader 应用在结果上的`loader`数组，可配置属性`loader、options、use`，为了兼容也可配置属性`query、loasers`
> 2. parser 为模块创建解析器的选项对象，可配置属性`parser`
>
> ***嵌套规则***
>
> 只有父规则匹配，才会计算这些规则，顺序如下
>
> 1. 父规则结果
> 2. rules
> 3. oneOf
>

- enforce

`string`

指定`loader`的类型，不指定正常加载

> 1. pre 前置
> 2. inlined 行内，应用于`import/require`
> 3. normal，不指定任何类型（不需要设置）
> 4. post 后置

加载器依次执行的两个阶段

> 1. Pitching phase 执行顺序 `post、inline、normal、pre`
> 2. Normal phase 执行顺序 `pre normal、inline、post`

覆盖加载器

> 1. 使用`!`前缀省略（覆盖）`normal`加载器
> 2. 使用`-!`前缀省略（覆盖）`normal、pre`加载器
> 3. 使用`!!`前缀省略（覆盖）`normal、pre、post`加载器

```javascript
// Disable normal loaders
import { a } from '!./file1.js';

// Disable preloaders and normal loaders
import { b } from  '-!./file2.js';

// Disable all loaders
import { c } from  '!!./file3.js';
```

***注意：***`inline loader`和`!`不应该使用，因为它们是非标准的

- exclude

配置需要排除的模块

- include

配置需要包含的模块

- issuer 

配置发起请求的资源文件

```javascript
// index.js
import A from 'a'
```

上面例子中，`index.js`是匹配到的文件。此选项可用于将加载器应用于特定模块或模块集的依赖项

- loader

`loader`是配置加载器，是`rule.use:[{loader}]`的简写方式，如果只需要用到一个`loader`，可以直接配置

- ~~loaders~~

`loaders`是配置多个加载器，是`rule.use`的别名

***注意：***`loaders`已废弃，请使用`rule.use`

- mimetype

使用`mimetype`匹配模块

```javascript
module: {
  rule: [{
  mimetype: 'application/json',
  type: 'json'
  }]
}
```

默认已包含的`mimetype`， `application/json`, `text/javascript`, `application/javascript`, `application/node` and `application/wasm`

- oneOf

只使用第一个匹配的规则

```javascript
module: {
  reule: [
  {
    test: /.css$/,
    oneOf: [
      {
        resourceQuery: /inline/, // foo.css?inline
        use: 'url-loader'
      },
      {
        resourceQuery: /external/, // foo.css?external
        use: 'file-loader'
      }
    ]
  }
  ]
}
```

- options / ~~query~~

`options` 和 `query` 是 `Rule.use: [ { options } ]` 的简写

***注意：***`query`已废弃，请使用`Rule.options`和 `UseEntry.options`

- parser

配置`模块化`解析功能，`noParse`针对`文件级别`，`parser`细粒化到文件内部`语法级别`

能用插件解析器配置如下：

> 1. false 禁用解析器
> 2. true 或不修改将其保留为 `undefined`，都是启用解析器

有些特殊的解析器不止有`true、false`两种配置，如内部插件`NodeStuffPlugin`可以接收一个对象 [参考](https://segmentfault.com/a/1190000013145176)

```javascript
module: {
  rules: [
  {
    test: /\.js$/,
    use: ['babel-loader'],
      parser: {
        amd: false, // 禁用 AMD
        commonjs: false, // 禁用 CommonJS
        system: false, // 禁用 SystemJS
        harmony: false, // 禁用 ES2015 Harmony import/export
        requireInclude: false, // 禁用 require.include
        requireEnsure: false, // 禁用 require.ensure
        requireContext: false, // 禁用 require.context
        browserify: false, // 禁用特殊处理的 browserify bundle
        requireJs: false, // 禁用 requirejs.*
        node: false, // 禁用 __dirname, __filename, module, require.extensions, require.main 等。
        node: {...} // 在模块级别(module level)上重新配置 node 层(layer)
      }
  }
  ]
}
```

如果`type`属性的值为`asset`。`parse`可以是一个对象或一个函数，用于描述是将文件内容编码为Base64还是将其作为单独的文件发送到输出目录的条件

如果`type`属性的值为`asset`或`asset/inline`。`generator`属性可以是描述模块源的编码的对象，也可以是通过自定义算法编码模块源的函数。

- parser.dataUrlCondition

`object = { maxSize number = 8096 }` `function (source, { filename, module }) => boolean`

配置模块`maxSize`，小于设定值将生成`dataUrl`注入到包里；大于设定值将输出到目录中

```javascript
module: {
  rules: [{
  parser: {
    dataUrlCondition: 4 * 1024
  }
  }]
}
```

当属性值为一个函数时，返回`true`将生成`dataUrl`注入到包里；`false`将输出到目录中

```javascript
module: {
  rules: {
  daataUrlCondition (source, { filename, module }) => {
    const content = source.toString();
    return content.includes('some marker');
  } 
  }
}
```

- generator.dataUrl

`object = { encoding string = 'base64' | false, mimetype string = undefined | false }` `function (content, { filename, module }) => string`

> ***值是一个对象时***
> 1. encoding 值为`base64`时，模块源码使用`base64`编码；值为`false`时，禁用编码
> 2. mimetype 设置`data Url`的`mimetype`，不设置从模块扩展获取
```javascript
module: {
 rules: [
   {
     generator: {
       encoding: 'base64',
       mimetype: 'mimetype/png'
     }
   }
 ]
}
```
> ***值是一个函数时，它会执行每个模块，并且返回一个`data Url`字符串***
```javascript
module: {
 rules: [
   {
     generator: (content) = {
       const svgToMiniDataURI = require('mini-svg-data-uri');
       if (typeof content !== 'string') {
         content = content.toString();
       }
       return svgToMiniDataURI(content);
     }
   }
 ]
}
```

- generator.filename

作用同`output.assetModuleFilename`，但它用于匹配模块，将会覆盖`output.assetModuleFilename`设置，并且它只能用于`asset`和 `asset/resource`类型模块

```javascript
module: {
  rules: [
    {
      test: /.png/,
      type: 'asset/resource'
    },
    {
      test: /.html/,
      type: 'asset/resource',
      generator: {
        filename: 'static/[hash][ext]'
      }
    }
  ]
}
```

- resource

匹配资源的条件，[参考](https://webpack.js.org/configuration/module/#rule-conditions)

- resourceQuery

通过查询字符串匹配模块。如想匹配`import Foo from './foo.css?inline'`，以下条件将匹配

```javascript
module: {
  rules: [
    {
      test: /\.css$/,
      resourceQuery: /inline/,
      use: 'url-loader'
    }
  ]
}
```

- parser.parse

`function(input) => string | object`

将模块以特定格式解析。如果`type`属性值为`json`，`parser.parse`将通过函数将模块转换为`javascript object`。可以用它将` toml, yaml and other non-JSON `文件做为`json`导入，没有特定的`loader`

```javascript
module: {
  rules: [
    {
      test: /.toml/
      type: 'json',
      parser: {
        parse: toml.parse
      }
    }
  ]
}
```

- rules

包含所以匹配`rule`的数组

- sideEffects

`bool`

配置模块是否应用`tree Shaking`特性

- test

设置条件匹配相应模块。不能与`resource`属性同时使用

- type

`string`

可能的值：`'javascript/auto' | 'javascript/dynamic' | 'javascript/esm' | 'json' | 'webassembly/sync' | 'webassembly/async' | 'asset' | 'asset/source' | 'asset/resource' | 'asset/inline'`

匹配特定类型的模块。防止默认规则及其它默认导入行为的发生，如你想在自定义的一个`loader`中匹配`json`文件，需要设置`type`值为`application/auto`来绕过`webpack`的内置`json`导入

```javascript
module: {
  rules: [
    {
      test: /\.json$/,
      type: 'application/auto',
      loader: 'custom-json-loader'
    }
  ]
}
```

- use

`[UseEntry]` `function(info)`

`use`是包含多个`UseEntry`对象的数组，每个`UseEntry`指定要使用的`loader`

> ***单个`loader`***
 ```javascript
//接收一个字符串
module: {
 rules: [
   use: ['style-loader']
 ]
}
//等于
module: {
 rules: [
   use: [{
    loader: 'style-loader'
   }]
 ]
}
 ```
> ***多个`loader`，将按从后向前执行（最后执行第一个）***
```javascript
module: {
  rules: [
    {
      //...
      use: [
        'style-loader',
        {
          loader: 'css-loader',
          options: {
            importLoaders: 1
          }
        },
        {
          loader: 'less-loader',
          options: {
            noIeCompat: true
          }
        }
      ]
    }
  ]
}
```
> ***`use`值为一个函数，参数是模块描述对象，返回`UseEntry`对象组成的数组（也可以返回简短形式如`use: [ 'style-loader' ]`），模块描述对象包含如下属性：***
>   - compiler 当前编译器
>   - issuer 导入被加载模块的模块的路径
>   - realResource 总是要加载的模块的路径
>   - resource 正在加载的模块的路径，它通常等于realResource，除非资源名添加了前缀`!=!`
```javascript
module: {
  rules: [
    {
      use: (info) =([
        {
          loader: 'custom-svg-loader'
        },
        {
          loader: 'svgo-loader',
          options: {
            plugins: [{
              cleanupIDs: { prefix: basename(info.resource) }
            }]
          }
        }
      ])
    }
  ]
}
```

- resolve

模块级别的`resolve`，它将`merge`最外层的`resolve`，例子

```javascript
// src/index.js
import footer from 'footer';
console.log(footer);
```

```javascript
// src/footer/default.js
export default 'default footer';
```

```javascript
// src/footer/overridden.js
export default 'overridden footer';
```

```javascript
// src/webpack.config.js
module.exports = {
  resolve: {
    alias: {
      'footer': './footer/default.js'
    }
  }
};
```

此时`index.js`将输出`default footer`，我们现在配置`rule.resolve`

```javascript
// src/webpack.config.js
module.exports = {
  resolve: {
    alias: {
      'footer': './footer/default.js'
    }
  },
  module: {
    rules: [
      {
        resolve: {
          alias: {
            'footer': './footer/overridden.js'
          }
        }
      }
    ]
  }
};
```

此时`index.js`将输出`default overridden`

***注意：***`webpack 4.36.1`才开始支持此配置项

- resolve.fullySpecified

`boolean = true`

当启用时，引用`.mjs`或者`package.js`中指定了`type`属性值为`module`时引用`.js`文件时。必须指定扩展名，否则`webpack`将编译失败，输出`Module not found`错误。并且`webpack`不会使用`resolve.mainFiles`解析文件目录，你必须自己指定文件名

```javascript
module: {
  rules: [
    {
      test: /.m?js/,
      resolve: {
        fullySpecified: false
      }
    }
  ]
}
```

***注意：***`fullySpecified`不会影响`mainFields、aliasFiles、aliases`的请求

#### 条件

- `字符串` 完全匹配，文件或者目录的绝对路径
- `正则表达式`
- `函数` 匹配函数输出，返回匹配真值
- `条件数组` 至少有一个条件必须匹配
- `对象` 所有属性必须匹配。每个属性都有一个已定义的行为。
- `{ and: [Condition] }` 所有条件必须匹配
- `{ or: [Condition] }` 匹配任何一个条件
- `{ not: [Condition] }` 所有条件必须不匹配

```javascript
const path = require('path');

module.exports = {
  //...
  module: {
    rules: [
      {
        test: /\.css$/,
        include: [
          path.resolve(__dirname, 'app/styles'),
          path.resolve(__dirname, 'vendor/styles')
        ]
      }
    ]
  }
};
```

#### useEntry

`object` `function(info)`

- 属性值是一个`对象`，此时必须有一个值为字符串的`loaser`属性；可以有一个值为对象的`options`属性（也可以是`query`，它是`options`的别名）

```javascript
module: {
  rules: [
    {
      loader: 'css-loader',
      options: {
        modules: true
      }
    }
  ]
}
```

- 属性值是一个`函数`，参考上面的`use`属性

```javascript
module.exports = {
  //...
  module: {
    rules: [
      {
        loader: 'file-loader',
        options: {
          outputPath: 'svgs'
        }
      },
      (info) => ({
        loader: 'svgo-loader',
        options: {
          plugins: [{
            cleanupIDs: { prefix: basename(info.resource) }
          }]
        }
      })
    ]
  }
};
```

#### ~~模块上下文~~

***避免使用这些选项，因为它们已被弃用并将很快被删除。***

```javascript
module.exports = {
  //...
  module: {
    exprContextCritical: true,
    exprContextRecursive: true,
    exprContextRegExp: false,
    exprContextRequest: '.',
    unknownContextCritical: true,
    unknownContextRecursive: true,
    unknownContextRegExp: false,
    unknownContextRequest: '.',
    wrappedContextCritical: false,
    wrappedContextRecursive: true,
    wrappedContextRegExp: /.*/,
    strictExportPresence: false // since webpack 2.3.0
  }
};
```

#### 参考

[webpack document Module](https://webpack.js.org/configuration/module/)

[webpack中文文档 Module](https://www.webpackjs.com/configuration/module/)

