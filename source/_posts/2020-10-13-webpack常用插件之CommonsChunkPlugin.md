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

#### 参考

[webpack——CommonsChunkPlugin](https://blog.csdn.net/zy444263/article/details/85252477)
[Webpack4之SplitChunksPlugin](https://juejin.im/post/6844903603400884238)
[详解CommonsChunkPlugin的配置和用法](https://segmentfault.com/a/1190000012828879)

