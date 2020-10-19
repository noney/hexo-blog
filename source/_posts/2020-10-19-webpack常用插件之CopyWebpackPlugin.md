---
title: webpack常用插件之CopyWebpackPlugin
date: 2020-10-19 10:41:35
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`copy-webpack-plugin`插件将已经存在的`单个文件`或`整个目录`（构建完成的文件）复制到构建目录

***注意：***

- `webpack-copy-plugin`，复制`node_module`中的文件（源文件）供构建过程中使用
- 如果希望`webpack-dev-server`在开发阶段，将文件写入到输出目录，需要强制设置 [`writeToDisk`](https://github.com/webpack/webpack-dev-middleware#writetodisk)选项或使用 [`write-file-webpack-plugin`](https://github.com/gajus/write-file-webpack-plugin)插件

#### 安装

```shell
yarn add copy-webpack-plugin --dev
```

<!-- more -->

#### 使用

```javascript
const CopyPlugin = require('copy-webpack-plugin');
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      { from: 'source', to: 'dest' },
      { from: 'other', to: 'public' },
    ],
    options: {
      concurrency: 100,
    },
  }),
  ],
};
```

#### Pattenrs

`Array` 规则

| 属性             | 值类型                      | 默认值                                      | 描述                                 |
| ---------------- | --------------------------- | ------------------------------------------- | ------------------------------------ |
| from             | String                      | undefined                                   | 文件来源                             |
| to               | String                      | compiler.options.output                     | 输出目录                             |
| context          | String                      | options.context 或 compiler.options.context | `from`的上下文路径                   |
| globOptions      | Object                      | undefined                                   | `glob`模式配置                       |
| filter           | Function                    | undefined                                   | 过滤掉不需要复制的文件               |
| toType           | String                      | undefined                                   | `to`字段的类型                       |
| force            | Boolean                     | false                                       | 覆盖`compilation.assets`编译完的文件 |
| flatten          | Boolean                     | false                                       | 删除所有目录引用，只复制文件名       |
| transform        | Function                    | undefined                                   | 编辑文件内容                         |
| cacheTransform   | Boolean 或 String 或 Object | false                                       | 是否启用缓存和配置它                 |
| transformPath    | Function                    | undefined                                   | 修改写入路径                         |
| noErrorOnMissing | Boolean                     | false                                       | 丢失文件时，是否产生错误             |

- from 文件来源。可以使用`Glob`模式或者`路径`表示。Glob可以接受 [fast-glob pattern-syntax](https://github.com/mrmlnc/fast-glob#pattern-syntax)规则且只能是一个字符串

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      'relative/path/to/file.ext',
      'relative/path/to/dir',
      path.resolve(__dirname, 'src', 'file.ext'),
      path.resolve(__dirname, 'src', 'dir'),
      '**/*',
    {
      from: '**/*',
    },
    // 如果glob模式描述的是一个绝对路径，我们需要使用`/`代替`\`，因为glob模式只能使用`/`
    path.posix.join(
      path.resolve(__dirname, 'src').replace(/\\/g, '/'),
      '*.txt'
    ),
    ],
  }),
  ],
};
```

***注意：***`\`在`unix`中表示一个有效字符，不是分隔符；在`window`中`\`和`/`都可以表示分隔符。所以`from`在`unix`中使用`/`表示分隔符；在`window`中使用`\`或`/`表示分隔符

- to 输出目录

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: '**/*',
        to: 'relative/path/to/dest/',
      },
      {
        from: '**/*',
        to: '/absolute/path/to/dest/',
      },
      {
        from: '**/*',
        to: '[path][name].[contenthash].[ext]',
      },
    ],
  }),
  ],
};
```

- context `from`的上下文路径。可以是`相对路径`或`绝对路径`，如果`context`是一个`相对路径`，将基于`compiler.options.context`的值得到`绝对路径`

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'src/*.txt',
        to: 'dest/',
        context: 'app/',
      },
    ],
  }),
  ],
};
```

***注意：***

1. 如果`from`是一个文件，`context`为该文件所在目录，结果为该文件
2. 如果`from`是一个目录，`context`为该目录，结果为该目录及层级结构
3. 如果`from`是一个`Glob`，无论`context`是什么，结果都为`from`指定的值

- globOptions `glob`模式配置。[参考](https://github.com/sindresorhus/globby#options)

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'public/**/*',
        globOptions: {
        dot: true,
        gitignore: true,
        ignore: ['**/file.*', '**/ignored-directory/**'],
        },
      },
    ],
  }),
  ],
};
```

- filter 过滤掉不需要复制的文件。要忽略文件请使用`globOptions.ignore`

```javascript
const fs = require('fs').promise;

module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'public/**/*',
        filter: async (resourcePath) => {
        const data = await fs.promises.readFile(resourcePath);
        const content = data.toString();

        if (content === 'my-custom-content') {
          return false;
        }

        return true;
        },
      },
    ],
  }),
  ],
};
```

- toType 输出目录的类型。`webpack`会自动确认类型，也可以手动指定

| 值       | 类型   | 默认值    | 描述                                                         |
| -------- | ------ | --------- | ------------------------------------------------------------ |
| dir      | String | undefined | 如果`to`没有扩展名或者`/`结尾，类型为目录                    |
| ile      | String | undefined | 如果`to`不是一个目录并且不是一个模板，类型为文件             |
| template | String | undefined | 如果`to`包含[模板点位符](https://github.com/webpack-contrib/file-loader#placeholders) |

```javascript
// 目录
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'path/to/file.txt',
        to: 'directory/with/extension.ext',
        toType: 'dir',
      },
    ],
  }),
  ],
};
```

```javascript
// 文件
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'path/to/file.txt',
        to: 'file/without/extension',
        toType: 'file',
      },
    ],
  }),
  ],
};
```

```javascript
// 模板
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'src/',
        to: 'dest/[name].[hash].[ext]',
        toType: 'template',
      },
    ],
  }),
  ],
};
```

- force 覆盖`compilation.assets`编译完的文件（通常其他插件/加载程序添加）

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'src/**/*',
        to: 'dest/',
        force: true,
      },
    ],
  }),
  ],
};
```

- flatten 删除所有目录引用，只复制文件名

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'src/**/*',
        to: 'dest/',
        flatten: true,
      },
    ],
  }),
  ],
};
```

- transform 编辑文件内容

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'src/*.png',
        to: 'dest/',
        // content参数为一个[`Buffer`](https://nodejs.org/api/buffer.html)对象，表示文件内容，可以使用content.toString()转换为字符串
        // absoluteFrom参考是一个字符串，表示文件的绝对路径
        transform(content, absoluteFrom) {
        return optimize(content);
        },
      },
    ],
  }),
  ],
};
```

- cacheTransform 是否启用缓存并配置它（默认缓存目录`node_modules/.cache/copy-webpack-plugin`）

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'src/*.png',
        to: 'dest/',
        transform(content, path) {
        return optimize(content);
        },
        // 启用transform缓存
        cacheTransform: true,
        // 启用transform缓存并配置缓存目录（必须是绝对路径）
        cacheTransform: path.resolve(__dirname, 'cache-directory'),
        // 启用transform缓存并配置缓存目录，同时配置缓存失效key
        cacheTransform: {
        directory: path.resolve(__dirname, 'cache-directory'),
        keys: {
          //对于基于外部值使缓存无效可能很有用
                //例如，您可以基于' process '无效缓存。' - {node: process。版本}
          key: 'value',
        },
        },
        // 启用transform缓存并配置缓存目录，同时使用函数返回缓存失效key
        cacheTransform: {
        directory: path.resolve(__dirname, 'cache-directory'),
        keys: (defaultCacheKeys, absoluteFrom) => {
          const keys = getCustomCacheInvalidationKeysSync();
          return {
          ...defaultCacheKeys,
          keys,
          };
        },
        },
        // 启用transform缓存并配置缓存目录，同时使用异步函数返回缓存失效key
        cacheTransform: {
        directory: path.resolve(__dirname, 'cache-directory'),
        keys: async (defaultCacheKeys, absoluteFrom) => {
          const keys = await getCustomCacheInvalidationKeysAsync();

          return {
          ...defaultCacheKeys,
          keys,
          };
        },
        },
      },
    ],
  }),
  ],
};
```

- transformPath 修改写入路径

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'src/*.png',
        to: 'dest/',
        transformPath(targetPath, absolutePath) {
        return 'newPath';
        },
      },
    ],
  }),
  ],
};
```

- noErrorOnMissing 丢失文件时，是否产生错误

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: path.resolve(__dirname, 'missing-file.txt'),
        noErrorOnMissing: true,
      },
    ],
  }),
  ],
};
```

#### Options

`Object`选项

| 属性        | 值类型 | 默认值 | 描述               |
| ----------- | ------ | ------ | ------------------ |
| concurrency | Number | 100    | 设置并发请求的数量 |

```javascript
module.exports = {
  plugins: [
    new CopyPlugin({
      patterns: [...patterns],
      options: { concurrency: 50 },
    }),
  ],
};
```

#### 例子

有以下`文件结构`

```
src/directory-nested/deep-nested/deepnested-file.txt
src/directory-nested/nested-file.txt
```
- form为一个Glob

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: 'src/directory-nested/**/*',
      },
    ],
  }),
  ],
};
```

结果为：

```
src/directory-nested/deep-nested/deepnested-file.txt,
src/directory-nested/nested-file.txt
```

- 如果仅想得到`src/directory-nested/`内容

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: '**/*',
        context: path.resolve(__dirname, 'src', 'directory-nested'),
      },
    ],
  }),
  ],
};
```

结果为：

```
deep-nested/deepnested-file.txt,
nested-file.txt
```

- form为一个目录

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: path.resolve(__dirname, 'src', 'directory-nested'),
      },
    ],
  }),
  ],
};
```

结果为：

```
deep-nested/deepnested-file.txt,
nested-file.txt
```

- form为一个文件

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: path.resolve(
        __dirname,
        'src',
        'directory-nested',
        'nested-file.txt'
        ),
      },
    ],
  }),
  ],
};
```

结果为：

```
nested-file.txt
```

- 忽略文件

```javascript
module.exports = {
  plugins: [
  new CopyPlugin({
    patterns: [
      {
        from: path.posix.join(
        path.resolve(__dirname, 'src').replace(/\\/g, '/'),
        '**/*'
        ),
        globOptions: {
          ignore: [
            // 忽略所有扩展名为 `txt` 文件
            '**/*.txt',
            // 忽略所有子目录为subdir的文件
            '**/subdir/**',
          ],
        },
      },
    ],
  }),
  ],
};
```

#### 参考

[webpack document CopyWebpackPlugin](https://webpack.js.org/plugins/copy-webpack-plugin/)