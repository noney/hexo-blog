---
title: webpack常用插件之EnvironmentPlugin
date: 2020-10-20 16:13:41
tags:
  - 日积月累
  - webpack

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`EnvironmentPlugin`是使用`DefinePlugin`设置`process.env`键的简写形式，它接收一个`键数组`或`对象`（键与默认值的映射）

```javascript
// 键数组
new webpack.EnvironmentPlugin(['NODE_ENV', 'DEBUG']);
// 等于
new webpack.DefinePlugin({
  'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV),
  'process.env.DEBUG': JSON.stringify(process.env.DEBUG)
});
```

<!-- more -->

***注意：***如果键是一个`不存在的环境变量`，将触发"`EnvironmentPlugin` - `${key}` environment variable is undefined"错误

对数为一个`对象`时，如果在 `process.env` 中对应的环境变量不存在时将使用指定的`默认值`

```javascript
new webpack.EnvironmentPlugin({
  NODE_ENV: 'development', // 除非有定义 process.env.NODE_ENV，否则就使用 'development'
  DEBUG: false
})
```

```javascript
// src/index.js
console.log(process.env.NODE_ENV, process.env.debug);
```

在`命令行`执行`NODE_ENV=production webpack --config ./webpack.config.js`，将得到结果：

```javascript
// dist/index.js
console.log("production", false); // process.env.NODE_ENV值为环境变量的值，debug为默认值
```

#### 与`DefinePlugin`不同

- 默认值将被`EnvironmentPlugin` 执行 `JSON.stringify`
- `DefinePlugin`会覆盖环境变量的值，`EnvironmentPlugin`只能设置默认值，只有在环境变量对应的健不存时才生效，修复上面的配置

```javascript
  new webpack.DefinePlugin({
    'process.env.NODE_ENV': JSON.stringify('development'),
    'process.env.DEBUG': JSON.stringify(false)
  })
```

输出结果：

```javascript
console.log("development", false);
```

#### DotenvPlugin

第三方插件`DotenvPlugin`允许导出 (a subset of) [dotenv variables](https://www.npmjs.com/package/dotenv):

```javascript
// .env
DB_HOST=127.0.0.1
DB_PASS=foobar
S3_API=mysecretkey
```

```javascript
new Dotenv({
  path: './.env', // Path to .env file (this is the default)
  safe: true // load .env.example (defaults to "false" which does not use dotenv-safe)
});
```

#### 参考

[webpack document EnvironmentPlugin](https://webpack.js.org/plugins/environment-plugin/)
[webpack中文文档 EnvironmentPlugin](https://www.webpackjs.com/plugins/environment-plugin/)