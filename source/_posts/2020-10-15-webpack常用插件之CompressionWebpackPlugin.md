---
title: webpack常用插件之CompressionWebpackPlugin
date: 2020-10-15 11:15:57
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

为`Content-Encoding`提供压缩版本

#### 安装

```shell
$ yarn add compression-webpack-plugin --dev
```

<!-- more -->

#### 选项

| 属性名             | 属性值类型                              | 默认值       | 描述                                                        |
| ------------------ | --------------------------------------- | ------------ | ----------------------------------------------------------- |
| test               | String 或 RegExp 或 Array<String 或 RegExp> | undefined    | 所有匹配的文件                                              |
| include            | String 或 RegExp 或 Array<String 或 RegExp> | undefined    | 需要包含的文件                                              |
| exclude            | String 或 RegExp 或 Array<String 或 RegExp> | undefined    | 需要排队的文件                                              |
| algorithm          | String 或 Function                      | gzip         | 压缩算法/功能                                               |
| compressionOptions | Object                                | { level: 9 } | 压缩算法的参数                                              |
| threshold          | Number                                | 0            | 只处理大于此设置的文件（单位k）                             |
| minRatio           | Number                                | 0.8          | 只处理压缩比大于此设置的文件（minRatio =压缩大小/原始大小） |
| filename           | String 或 Function                      | [path]\[base].gz    | 生成的文件名模板 |
| deleteOriginalAssets | Boolean | false | 是否删除未压缩的源文件 |
| cache | Boolean 或 String | true | 是否启用缓存（`webpack 5`中已避免使用，使用[cache](https://webpack.js.org/configuration/other-options/#cache)代替） |

#### 使用

```javascript
module.exports = {
  plugins: [
  new CompressionPlugin({
    test: /\.js(\?.*)?$/i,
    exclude: /\/excludes/,
    exclude: /\/excludes/,
    /*使用自定义函数算法，则`compressionOptions`的默认值为`{}`*/
    algorithm(input, compressionOptions, callback) {
      return compressionFunction(input, compressionOptions, callback);
    },
    compressionOptions: { level: 1 },
    threshold: 8192,
    minRatio: 0.8,
    filename(pathData) {
      // The `pathData` argument contains all placeholders - `path`/`name`/`ext`/etc
      // Available properties described above, for the `String` notation
      if (/\.svg$/.test(pathData.file)) {
        return 'assets/svg/[path][base].gz';
      }
      return 'assets/js/[path][base].gz';
    },
    deleteOriginalAssets: true,
    // 值为字符串目录时，表示开启缓存同时设置缓存文件存放目录
    // 默认的缓存目录`node_modules/.cache/compression-webpack-plugin`
    cache: 'path/to/cache'
  }),
  ],
};
```

#### minRatio

`minRatio`设置值说明

- 设置`minRatio`为`1`处理所有比原始值小的文件
- 设置`minRatio`为`Infinity`，处理所有文件，包括比原始值大的文件，或者原始字节为`0`的文件
- 设置`minRatio`为`Number.MAX_SAFE_INTEGER`，处理所有文件，但不包括原始字节为`0`的文件

#### filename

`filename`中占位符对应的值如下（`assets/images/image.png?foo=bar#hash`）

- [path] 文件目录包括`/`（`assets/images/`）
- [file] 文件路径（`assets/images/image.png`）
- [base] 文件名加扩展名（`[name]` + `[ext]`）(`image.png`)
- [name] 文件名（`image`）
- [ext] 文件扩展名（`.png`）
- [query] 查询包括`?`（`?foo=bar`）
- [fragment] 片段（#hash）

#### 参考

[webpack document CompressionWebpackPlugin](https://webpack.js.org/plugins/compression-webpack-plugin/)

  

