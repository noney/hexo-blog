---
title: webpack详解之optimization
date: 2020-10-10 14:29:58
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`webpack 4`开始会根据不同`模式`内置相应的`优化配置`（有效的简化的复杂配置），但还是支持`手动配置`和对内置优化配置的`覆盖`

#### 最小化

- minimize `boolean = true` 设置是否使用 [TerserPlugin](https://webpack.js.org/plugins/terser-webpack-plugin/)插件对`bundle`进行`最小化`处理，当值为`true`时，还可以使用`optimization.minimizer`配置覆盖默认最小化设置

```javascript
optimization: {
  minimize: true
}
```

<!-- more -->

- minimizer [TerserPlugin]` and or `[function (compiler)] 使用`一个`或`多个` `TerserPlugin`实例覆盖默认最小化设置

```javascript
// 实例
optimization: {
  minimizer: [
      new TerserPlugin({
        cache: true,
        parallel: true,
        sourceMap: true, // Must be set to true if using source-maps in production
        terserOptions: {
          // https://github.com/webpack-contrib/terser-webpack-plugin#terseroptions
        }
      }),
    ],
}
```

```javascript
// function
optimization: {
  minimizer: [
      (compiler) => {
        const TerserPlugin = require('terser-webpack-plugin');
        new TerserPlugin({ /* your config */ }).apply(compiler);
      }
    ]
}
```

```javascript
// 使用`...`表示默认最小化设置
optimization: {
  minimizer: [new CssMinimizer(), '...']
}
```

#### splitChunks

`object`

从`webpack v4+`开始，使用`optimization.splitChunks`替换`CommonsChunkPlugin`来处理`通用模块`，具体配置参考 [SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)

#### runtimeChunk

`object` `string` `boolean`

- 值为`true` 或 `'multiple'`时，为每个入口额外添加一个只包含运行时的`chunk`，相当于

```javascript
optimization: {
  runtimeChunk: {
    name: entrypoint => `runtime~${entrypoint.name}`
  }
}
```

- 值为`'single'`时，创建一个运行时的`chunk`，供所有入口共享，相当于

```javascript
optimization: {
  runtimeChunk: {
    name: 'runtime'
  }
}
```

- 值为一个`object`，只会有`name`一个属性，该属性表示运行时块的名称
- 默认值为`false`，每个入口嵌入运行时

#### emitOnErrors

`boolean = false`

当值为`true`时，在编译错误的时候，会将错误带到代码中，将会在运行时触发错误

```javascript
optimization: {
  emitOnErrors: true
}
```

***注意：***如果使用`webpack cli`，`emitOnErrors`值为`true`时，编译错误时将不会中断编译

#### namedModules

`boolean = false`

设置可读的模块标识符用于调试，如果没有明确设置，`webpack`默认将在`开发模式`中启用；`生产模式`中禁用

```javascript
optimization: {
  namedModules: true
}
```

#### namedChunks

`boolean = false`

设置可读的`chunk`标识符用于调试，如果没有明确设置，`webpack`默认将在`开发模式`中启用；`生产模式`中禁用

```javascript
optimization: {
  namedChunks: true
}
```

#### moduleIds

`boolean = false` `string: 'natural' | 'named' | 'deterministic' | 'size'`

使用哪种算法生成`module ids`，默认值为`false`，不使用任何内置算法，可以通过提供的插件生成，支持下列值

| 值            | 描述                                |
| ------------- | ----------------------------------- |
| natural       | 按顺序的数字                        |
| named         | 可读的`id`，用于调试                |
| deterministic | 被`hash`成小数值的模块名称          |
| size          | 专注于让初始下载包大小更小的数字 id |

```javascript
// 使用插件生成moduleids
module.exports = {
  //...
  optimization: {
    moduleIds: false
  },
  plugins: [
    new webpack.ids.DeterministicModuleIdsPlugin({
      maxLength: 5
    })
  ]
};
```

***注意：***在`webpack 5`中，添加了 `deterministic`；删除了`total-size`；不赞成使用`hashed`

#### chunkIds

`boolean = false` `string: 'natural' | 'named' | 'size' | 'total-size' | 'deterministic'`

使用哪种算法生成`chunk ids`，默认值为`false`，不使用任何内置算法，可以通过提供的插件生成，有关联的默认值

- optimization.occurrenceOrder启用时，optimization.chunkIds值为`total-size`
- optimization.namedChunks启用时，optimization.chunkIds值为`named`
- `开发模式`，optimization.chunkIds值为`named`；`生产模式`，optimization.chunkIds值为`deterministic`
- 如果以上条件都不满足，optimization.chunkIds默认值为`natural`

| 值            | 描述                                |
| ------------- | ----------------------------------- |
| natural       | 按顺序的数字                        |
| named         | 可读的`id`，用于调试                |
| deterministic | 被`hash`成小数值的模块名称          |
| size          | 专注于让初始下载包大小更小的数字 id |
| total-size    | 专注于让总下载包大小更小的数字 id   |

```javascript
optimization: {
  chunkIds: 'named'
}
```

***注意：***`chunkIds`默认值为`deterministic`，最小长度为`3`的数字。如果想覆盖默认值，需要将`optimization.chunkIds`设置为`false`，并使用`webpack.ids.DeterministicChunkIdsPlugin`插件

```javascript
module.exports = {
  //...
  optimization: {
    chunkIds: false
  },
  plugins: [
    new webpack.ids.DeterministicChunkIdsPlugin({
      maxLength: 5
    })
  ]
};
```

#### nodeEnv

`boolean = false` `string`

设置`process.env.NODE_ENV`变量值，如果使用了[DefinePlugin](https://webpack.js.org/plugins/define-plugin/) 插件，除非`nodeEvn`值为`false`，否则`process.env.NODE_ENV`的值取决于插件的设置，`nodeEnv`默认值取决于`mode`属性（如果`mode`值为`none`，`nodeEvn`值为`false`，此时`process.env.NODE_ENV`的值为`none`）

```javascript
optimization: {
  nodeEnv: 'development'
}
```

***注意：***当`nodeEnv`值为`false`时，不会修改/设置`process.env.NODE_ENV`变量值

#### mangleWasmImports

`boolean = false`

值为`true`时，将导入的字符串变短使`wasm`体积变小

```javascript
optimization: {
  mangleWasmImports: true
}
```

#### removeAvailableModules

`boolean = false`

值为`true`时，`webpack`将从`chunks`中检测并且删除已经包含在所有父模块中的模块。***生产环境会默认开启***

```javascript
optimization: {
  removeAvailableModules: true
}
```

***注意：***`removeAvailableModules`会降低`webpack`构建性能，在下一个主要版本中默认将禁用它，如果想提高构建性能，需要在生产环境禁用它

#### removeEmptyChunks

`boolean = true`

值为`true`时，`webpack`将从检测并删除空的`chunks`

```javascript
optimization: {
  removeEmptyChunks: false
}
```

#### mergeDuplicateChunks

`boolean = true`

值为`true`时，`webpack`将合并相同内容的`chunks`

```javascript
optimization: {
  mergeDuplicateChunks: false
}
```

#### flagIncludedChunks

`boolean`

值为`true`时，标记`chunks`的子集，当更大的块被加载时，子集不会被加载。***生产环境会默认开启***

```javascript
optimization: {
  flagIncludedChunks: true 
}
```

#### occurrenceOrder

`boolean`

值为`true`时，计算出模块的顺序，从而得到最小的初始`bundle`。***生产环境会默认开启***

```javascript
optimization: {
  occurrenceOrder: false 
}
```

#### providedExports

`boolean`

值为`true`时，找出模块`exports`了哪些接口，以便使用`export * from ....`生成更高效的代码。默认值为`true`

```javascript
optimization: {
  providedExports: false 
}
```

#### usedExports

`boolean = true` `string: 'global'`

值为`true`时，会标记出每个模块被使用的接口，依赖于`providedExports`，供其它优化使用，如未使用的接口不会被导出或被删除

```javascript
optimization: {
  usedExports: false 
}
// 从每个运行时分析出导出接口
optimization: {
  usedExports: 'global' 
}
```

#### concatenateModules

`boolean`

值为`true`时，会将可以安全合并的模块片段合并。可以减少构建包体积，依赖于`providedExports`和`usedExports`

```javascript
optimization: {
  concatenateModules: true
}
```

#### sideEffects

`boolean = true`

根据`package.json`的`sideEffects`标记或规则，来决定是否导出`不被使用并且被标记为不含副作用的模块` [参考](https://zhuanlan.zhihu.com/p/44053307)

```json
// package.json
{
  "name": "awesome npm module",
  "version": "1.0.0",
  "sideEffects": false
}
```

***注意：***`sideEffects`应该设置在`npm`包的`package.json`中，并不意味着你的普通项目需要设置它

`sideEffects`生效必须设置`providedExports`为启用。它虽然会增加构建时间，但会导出更少的代码

```javascript
optimization: {
  sideEffects: true
}
```

#### portableRecords

`boolean`

值为`true`时，会生成相对路径的记录，以便移动上下文文件夹。默认情况是禁用的，如果至少提供了一个记录选项 [`recordsPath`](https://webpack.js.org/configuration/other-options/#recordspath), [`recordsInputPath`](https://webpack.js.org/configuration/other-options/#recordsinputpath), [`recordsOutputPath`](https://webpack.js.org/configuration/other-options/#recordsoutputpath)，则自动启用

```javascript
optimization: {
  portableRecords: true
}
```

#### mangleExports

`boolean` `string: 'deterministic' | 'size'`

控制导出接口名字。`生产环境`下值为`deterministic`，其它模式下禁用，下面是允许的值

| 值            | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| size          | 简写形式 — 通常只有一个字符 — 专注于最小的下载 size          |
| deterministic | 简写形式 - 通常两个字符 — 在添加或移除 export 时不会改变。适用于长效缓存。 |
| true          | 等价于`deterministic`                                        |
| false         | 保留原名，有利于阅读和调试。                                 |

```javascript
optimization: {
  mangleExports: true
}
```

#### innerGraph

`boolean = true`

值为`true`时，会对未使用的导出内容，进行内部图分析

```javascript
optimization: {
  innerGraph: false
}
```

#### realContentHash

`boolean = true`

值为`true`时，使用`assets`文件内容生成`hash`，否则，使用内部计算方法生成`hash`（相同内容的`assets`会生成不同的`hash`）

```javascript
optimization: {
  realContentHash: false
}
```

#### 参考

[webpack document Optimization](https://webpack.js.org/configuration/optimization/)
[webpack中文文档 Optimization](https://webpack.docschina.org/configuration/optimization/)