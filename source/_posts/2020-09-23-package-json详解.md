---
title: package.json详解
date: 2020-09-23 12:09:13
tags:
	- 日积月累
	- 工具

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`package.json`是使用`npm`项目的配置文件。里面包含项目的`名字、版本信息、模块依赖`等的信息描述

#### name

模块名称，它和`version`构成一个标识，如果是要发布的包，这个标识必须是唯一的，它的命名规则如下

- 长度小于等于`214`个字符
- 不能以`.`和`_`开头
- 新包的名称不能包含`大写字母`
- 名称将有可能做为`URL的一部分、命令行上的参数和文件夹名称`。因此，名称不能包含任何非`url`安全字符。

```json
"name": "uploadbigfile"
```

<!-- more -->

#### version

模块版本号，它必须能被 [node-semver](https://github.com/isaacs/node-semver)解析

```json
"version": "0.0.1"
```

#### description

模块描述，是一个字符串，可以通过它在[npmjs](https://www.npmjs.com/)中搜索出发布的包

```json
"description": "基于`node`构建的大文件分块上传工具"
```

#### keywords

模块关键字，是一个字符串数组，可以通过它在[npmjs](https://www.npmjs.com/)中搜索出发布的包

```json
"keywords": ["node", "upload", "split"]
```

### homepage

模块首页，是一个`url`地址

```json
"homepage": "https://github.com/noney/uploadBigFile"
```

#### bugs

跟踪模块问题的`url`和`email`，遇到问题可以通过它们得到帮助（如果只指定`url`，则`bugs`的值是一个字符串）

```json
"bugs": {
	"url": "https://github.com/noney/uploadBigFile/issues",
	"email": "noneychrome@gmail.com"
}
"bugs": "https://github.com/noney/uploadBigFile/issues"
```

#### license

软件许可，请参考[开源许可证教程](http://www.ruanyifeng.com/blog/2017/10/open-source-license-tutorial.html)

#### author、contributors

模块参与人，`author`指一个人，`contributors`是多个人的数组

- 对象形式

```json
"author": {
	"name": "noneyli",
	"email": "noneychrome@gmail.com",
	"url": "http://0kv30q.coding-pages.com/"
}
```

- 字符串形式

```json
"author": "noneyli <noneychrome@gmail.com> (http://0kv30q.coding-pages.com/)"
```

***注意：*** `email、url`字段都是可选的，`npm`还提供了一个`maintainers`字段，表示顶级维护者

#### files

发布模块时要包含的文件`数组`，语法同`.gitignore`，省略该字段它的默认值为`[*]`，即包含所有文件。`.npmignore`文件与其功能正好相反，排除要包含的文件。

以下文件不受上面限制，默认被`包含`

- `package.json`
- `README`
- `CHANGES` / `CHANGELOG` / `HISTORY`
- `LICENSE` / `LICENCE`
- `NOTICE`
- The file in the “main” field

以列文件不受上面限制，默认被`排除`

- `.git`
- `CVS`
- `.svn`
- `.hg`
- `.lock-wscript`
- `.wafpickle-N`
- `.*.swp`
- `.DS_Store`
- `._*`
- `npm-debug.log`
- `.npmrc`
- `node_modules`
- `config.gypi`
- `*.orig`
- `package-lock.json` (use shrinkwrap instead)

#### main

模块入口，比如安装了`uploadbigfile`模块，可以使用`require('uploadbigfile')`调用该模块，主模块的`exports`对象将被返

```json
"main": "index.js"
```

#### browser

定义模块`浏览器`环境下引用的入口，如果定义的模块，要在`浏览器`端调用，使用`browser`字段，而不使用`main`字段

```json
"browser": "index.js"
```

#### module

`es module`规范的模块的入口

```json
"module": "index.js"
```

#### main & browser & module区别

请参考[[package.json 中 你还不清楚的 browser，module，main 字段优先级](https://segmentfault.com/a/1190000019438150)]

#### bin

指定可执行文件，`npm`安装模块时，公用模块会使用软链链接到`prefix/bin`目录下，本地模块会使用软链链接到`./node_modules/.bin/`目录下

```json
"bin": {"uploadbigfile": "./index.js"}
```

安装成功后，`npm`会创建一个软链`/usr/local/bin/uploadbigfile`指向模块`index.js`

上面方法也可以指定多个`可执行文件`，如果你只有一个`可执行文件`，并且这的名称也是模块的名称，也可以使用字符串方式指定

```json
"bin": "/path/to/uploadbigfile"
```

***注意：***请确保你的`可执行文件`顶部包含`#!/usr/bin/env/node`

#### man

指定一个文件或文件名数组，以便放置`man`程序来查找。

- 如果只提供了一个文件，执行`man <pkgname>`显示`/man/doc.1`文件

```json
"man": "./man/doc.1"
```

- 多个文件，并且文件名不是包名

```json
"man": [ "./man/uploadbigfile.1", "./man/bar.1" ]
```

将通过`man uploadbigfile 、man uploadbigfile-bar`命令显示

- `man`文件必须以数字结尾，如果被压缩，还可以选择`.gz`后缀

```json
"man": [ "./man/uploadbigfile.1", "./man/uploadbigfile.2" ]
```

将通过`man uploadbigfile 、man 2 uploadbigfile`命令显示

#### directories

`CommonJS`规范使用`directories`来描述模块的结构，目前还没有什么具体作用，在将来，这些信息可能用于其它方面

- lib 告诉模块使用者`lib`目录位置
- bin 作用同顶层`bin`字段，如果只有一个`可执行文件`，使用顶层`bin`；如果有多个可执行文件，想同时被添加，使用`directories.bin`指定目录
- man 作用同项层`man`字段，指定多个`man`文件时使用
- doc 把`markdown`文件放在这里，将来可能会用到
- example 把示例`script`放在这里， 将来可能会用到
- test 把测试代码放在这里，将来可能会用到

#### repository

模块代码存放位置

```json
{
	"type": "git",
	"url": "https://github.com/noney/uploadBigFile"
}
```

对于GitHub、GitHub gist、Bitbucket或GitLab库，您可以使用与安装npm时相同的快捷语法:

```json
"repository": "npm/npm"
"repository": "github:user/repo"
"repository": "gist:11081aaa281"
"repository": "bitbucket:user/repo"
"repository": "gitlab:user/repo"
```

如果`package.json`不在根目录，可以通过`directory`字段，指定位置

```json
{
	"type": "git",
	"url": "https://github.com/noney/uploadBigFile",
	"directory": "packages/react-dom"
}
```

#### scripts

运行脚本命令的映射，请参考[npm-scripts](https://docs.npmjs.com/misc/scripts)

```json
"scripts": {
	"start": "node ./index.js"
}
```

#### config

设置模块脚本中使用的配置参数

```json
"config": {
	"port": "8080"
}
```

脚本文件中可以调用`npm_package_config_port`环境变量，来获取`8080`。然后用户可以使`npm config set uploadbigfile:port 8001 `覆盖它。参考[npm-config](https://docs.npmjs.com/misc/config)和[npm-scripts](https://docs.npmjs.com/misc/scripts)

#### dependencies

指定模块依赖关系，***用于生产环境***。由多个包名与版本范围映射组成的对象。版本范围是由一个或多个空格分隔的字符串，依赖关系也可以通过`tarball`或`git URL`来标识

版本范围请参考 [semver](https://docs.npmjs.com/misc/semver)（x.y.z 主版本.次版本.补丁版本）

- version 必须`完全匹配`版本

```json
"uploadBigFile": "1.0.0"
```

- \>version 必须`大于`指定版本

```json
"uploadBigFile": ">1.0.0"
```

- \>=version 必须`大于等于`指定版本

```json
"uploadBigFile": ">=1.0.0"
```

- < version 必须`小于`指定版本

```json
"uploadBigFile": "<1.0.0"
```

- <=version 必须`小于等于`指定版本

```json
"uploadBigFile": "<=1.0.0"
```

- \~version 如果指定了`次版本`，则只允许更新`补丁版本`；如果没指定，允许更新`次版本、补丁版本`

| 描述          | 范围                                       | 备注                                         |
| ------------- | ------------------------------------------ | -------------------------------------------- |
| ~1.2.3        | `>=1.2.3 <1.(2+1).0` 等于 `>=1.2.3 <1.3.0` | 指定了`次版本`，允许更新`补丁版本`           |
| ~1.2          | `>=1.2.0 <1.(2+1).0` 等于 `>=1.2.0 <1.3.0` | 同上，相当于`~1.2.0`                         |
| ~1            | `>=1.0.0 <(1+1).0.0` 等于 `>=1.0.0 <2.0.0` | 没有指定`次版本`，允许更新`次版本、补丁版本` |
| ~1.2.3-beta.2 | `>=1.2.3-beta.2 <1.3.0`                    |                                              |

```json
"uploadBigFile": "~1.2.3"
```

- ^version 更新至左边非`0`位的下一个版本；缺失的值变为`0`，如果缺失位前一位为`0`，允许更新至前一位的下一个版本，否则，执行非缺失规则

| 描述          | 范围                    | 备注                                                         |
| ------------- | ----------------------- | ------------------------------------------------------------ |
| ^1.2.3        | `>=1.2.3 <2.0.0`        | `主版本`不为`0`，`主版本`加`1`，后续版本为`0`，即允许更新到`2.0.0`（不包含`2.0.0`） |
| ^0.2.3        | `>=0.2.3 <0.3.0`        | `次版本`不为`0`，`次版本`加`1`，后续版本为`0`，即允许更新到`0.3.0`（不包含`2.3.0`） |
| ^0.0.3        | `>=0.0.3 <0.0.4`        | `补丁版本`不为`0`，`补丁版本`加`1`，即允许更新到`0.0.4`（不包含`0.0.4`） |
| ^1.2.3-beta.2 | `>=1.2.3-beta.2 <2.0.0` |                                                              |
| ^0.0.3-beta   | `>=0.0.3-beta <0.0.4`   |                                                              |
| ^1.2.x        | `>=1.0.0 <2.0.0`        | 缺失位为`补丁位`，它的前一位不为`0`，执行非缺失规则          |
| ^0.0.x        | `>=0.0.0 <0.1.0`        | 缺失位为`补丁位`，它的前一位为`0`，允许更新至`次版本`下一个版本 |
| ^0.0          | `>=0.0.0 <0.1.0`        | 同`^0.0.x`                                                   |
| ^1.x          | `>=1.0.0 <2.0.0`        | `次版本、补丁版本`均缺失，它的前一位不为`0`，执行非缺失规则  |
| ^0.x          | `>=0.0.0 <1.0.0`        | `次版本、补丁版本`均缺失，它的前一位为`0`，允许更新`主版本`下一个版本 |

```json
"uploadBigFile": "^1.2.3"
```

- 使用`x`或者`*`代替版本号，允许更新至缺位前一位的下一个版本

| 描述  | 范围             | 备注          |
| ----- | ---------------- | ------------- |
| *     | `>= 0.0.0`       | 可以写成`""`  |
| 1.x   | `>=1.0.0 <2.0.0` | 可以写成`1`   |
| 1.2.x | `>=1.2.0 <1.3.0` | 可以写成`1.2` |

- http://... `URL`作为依赖项（可以指定一个压缩包`url`代替版本范围，安装时会通过这个`url`下载安装到本地）

```json
"uploadBigFile": "http://asdf.com/asdf.tar.gz"
```

- version1 - version2 允许更新至`>=version1 <=version2`的版本

| 描述          | 范围              | 备注                                           |
| ------------- | ----------------- | ---------------------------------------------- |
| 1.2.3 - 2.1.2 | `>=1.2.3 <=2.1.2` |                                                |
| 1.2 - 2.1.2   | `>=1.2.0 <=2.1.2` | 前面项缺失，补`0`                              |
| 1.2.3 - 2     | `>=1.2.3 <=3.0.0` | 后面项缺失，允许更新至缺失项前一位的下一个版本 |

```json
"uploadBigFile": "1.0.0 - 2.0.0"
```

- range1 || range2 允许更新至两个区间的版本

```json
"uploadBigFile": "~1.0.0 || ^0.5.x || 1.3.x"
```

- git... `Git URL`作为依赖项

> <protocol>://[<user>[:<password>]@]<hostname>[:<port>][:][/]<path>[#<commit-ish> | #semver:<semver>]
>
> <protocol>可以是`git，git+ssh，git+http，git+https，git+file中`的任何一个
>
> #<commit-ish> 指定`commit`
>
> #semver:<semver> `<semver>`可以是任何有效的`semver`范围，用来指定`tags`或者`refs`
>
> 如果`[#<commit-ish> | #semver:<semver>]`都没有指定，将下载`master`分支的代码

```json
"uploadBigFile": "git+ssh://git@github.com:npm/cli.git#v1.0.27"
"uploadBigFile": "git+ssh://git@github.com:npm/cli#semver:^5.0"
"uploadBigFile": "git+https://isaacs@github.com/npm/cli.git"
"uploadBigFile": "git://github.com/npm/cli.git#v1.0.27"
```

- user/repo 在`1.1.65`版本中，可以使用`GitHub urls`作为依赖项，与`git url`一样，也可以包含后缀

```json
"express": "expressjs/express"
"mocha": "mochajs/mocha#4727d357ea"
"module": "user/repo#feature\/branch"
```

- tag 一个已发布的`tag`版本作为依赖项[npm-dist-tag](https://docs.npmjs.com/cli/dist-tag)

```json
"uploadBigFile": "latest"
```

- `path/path/path`  在`2.0.0`版本中，可以使用本地路径做为依赖项，本地路径可以使用`npm install -s`或者`npm install -save`保存

```
../foo/bar
~/foo/bar
./foo/bar
/foo/bar
```

它们将被规范化为一个相对路径添加到`package.json`中

```json
"uploadBigFile": "file:../foo/bar"
```

这个特性对于本地离线开发和创建需要安装npm的测试很有帮助，这些测试需要安装在您不想触及外部服务器的地方，但是在将包发布到公共注册表时不应该使用。

#### devDependencies

指定模块依赖关系，***用于开发环境***。有些依赖比如`css`预处理、`babel`转换等都只有在开发环境才会用到，我们就可以把它映射到`devDependency`。***当别人在自己的程序中使用我们的模块的时候，`devDependency`是不需要下载的***

```json
"dependencies": {
	"babel-loader": "^7.1.5" 
}
```

#### peerDevDependency

指定当前模块需要的依赖，而这些依赖由使用该模块的主模块安装。[参考](https://www.cnblogs.com/wonyun/p/9692476.html)

```json
"peerDependencies": {
	"react": ">=16.0.0",
	"react-dom": ">=16.0.0"
}
```

***注意：***

- `npm1、npm2`不需要在使用主模块的依赖中指定，它会`强制安装`；在`npm3`中会`发出警告`，需要主模块主动添加相应依赖
- 为了避免主模块安装时发生冲突，尽量不要局限到`补丁版本`

#### bundledDependencies

`bundledDependencies`指定一个包名数组，它有两个作用 [参考](https://segmentfault.com/a/1190000008398819)

- 安装时，在`node_module`文件夹下面创建一个与包名同名的文件夹，文件夹里是`包名数组`里的依赖项，便于用户管理

```json
"bundledDependencies": ["react", "core-js"]
```

安装完成的文件结构

```json
├── node_modules
		├── uploadBigFile
		│   └── react
		│   └── core-js
		└── loadsh
```

- 通过`npm pack`命令生成一个`tarball`，名字是`uploadBigFile-v1.0.0.tgz`。可以通过`npm install uploadBigFile-v1.0.0.tgz `在其它项目中安装它，安装完之后在新项目就会包含`react、core-js`两个依赖

***注意：***

- 不需要在`包名数组`中指定版本范围，因为在`dependencies`依赖中已经指定过了
- `bundleDependencies`是它的别名

#### optionalDependencies

可选依赖，即使安装失败也不影响模块运行，也不会导致`npm`安装失败。

```json
"optionalDependencies": {
	"jquery": "^3.5.1"
}
```

既然会有失败情况，所以我们在使用可选模块时必须做异常处理

```javascript
const $ = null;
try {
	$ = require('jquery');
}
if ($ !== null) {/*...*/}
```

***注意：*** `optionalDependencies`会覆盖`dependencies`同名依赖，所以不要同时出现在这两个依赖中

#### engines

指定模块运行环境，不指定默认值为`*`，表示不限制运行环境

```json
"engines" : { "node" : ">=0.10.3 <0.12" }
```

还可以指定使用哪个`npm`安装模块

```json
"engines" : { "npm" : "~1.0.20" }
```

***注意：***如果没有设置`engine-strict`标志，环境不符合时，只是在安装时产生警告

#### engineStrict

~~engineStrict特性将在`npm3`中删除~~

设置`engineStrict`，环境不符合时，模块将安装失败

#### OS

指定模块运行在哪些操作系统中

```json
"os" : [ "darwin", "linux" ]
```

在系统名称前添加`!`，指定模块不能运行在哪些操作系统中

```json
"os" : [ "!win32" ]
```

***注意：***系统名称通过`process.platform`获取

#### cpu

指定模块运行在哪些`cpu`架构中

```json
"cpu" : [ "x64", "ia32" ]
```

在`cpu`架构名称前添加`!`，指定模块不能运行在哪些`cpu`架构中

```json
"cpu" : [ "!arm", "!mips" ]
```

***注意：***系统名称通过`process.arch`获取

#### ~~preferGlobal~~

已弃用

此选项用于触发npm警告，但不再发出警告。它的存在纯粹是为了提供信息。现在建议您尽可能将任何二进制文件安装为本地devDependencies。

#### private

将模块设为私有，`npm`将不会发布它。如果你只想发布的特定的注册表（比如内部注册表），可以使用`publishConfig`配置覆盖它

```json
"private": true
```

#### publishConfig

模块私有发布的配置，具体参考[npm-config](https://docs.npmjs.com/misc/config)

#### 参考

[探讨npm依赖管理之peerDependencies](https://www.cnblogs.com/wonyun/p/9692476.html)
[聊聊 node.js 中各种 dependency](https://segmentfault.com/a/1190000008398819)
[.npmignore、.gitignore和package.json中的files字段](https://segmentfault.com/a/1190000020841543?utm_source=tag-newest)
[package.json详解](http://www.sosout.com/2018/08/08/package-json-module.html)
[package.json 中 你还不清楚的 browser，module，main 字段优先级](https://segmentfault.com/a/1190000019438150)
[npm-package.json](https://docs.npmjs.com/files/package.json#preferglobal)