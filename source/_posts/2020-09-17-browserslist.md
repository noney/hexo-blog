---
title: browserslist
date: 2020-09-17 17:26:04
tags:
  - 日积月累
  - 工具

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

[browserslist](https://github.com/browserslist/browserslist)为前端工具提供目标`浏览器、node`的范围版本，数据来自 [Can I Use](http://caniuse.com/) 网站

#### 使用

`borwserslist`可以在下列工具中使用

- [Autoprefixer](https://github.com/postcss/autoprefixer)
- [Babel](https://github.com/babel/babel/tree/master/packages/babel-preset-env)
- [postcss-preset-env](https://github.com/jonathantneal/postcss-preset-env)
- [eslint-plugin-compat](https://github.com/amilajack/eslint-plugin-compat)
- [stylelint-no-unsupported-browser-features](https://github.com/ismay/stylelint-no-unsupported-browser-features)
- [postcss-normalize](https://github.com/jonathantneal/postcss-normalize)
- [obsolete-webpack-plugin](https://github.com/ElemeFE/obsolete-webpack-plugin)

<!-- more -->

#### 配置方式

`browserslist`会按下面顺序获取配置

- 在`package.json`

```json
 "browserslist": [
    "defaults",
    "not IE 11",
    "not IE_Mob 11",
    "maintained node versions"
  ]
```

-`.browserslistrc`配置文件（条件使用`新行`分隔。此时条件使用`or`组合器结合。注释以#符号开头）

```
# Browsers that we support

defaults
not IE 11
not IE_Mob 11
maintained node versions
```

- `browserslist`配置文件

- `BROWSERSLIST`环境变量

- 如果通过以上都无法获取配置，`browserslist`将使用`default`配置`> 0.5%, last 2 versions, Firefox ESR, not dead`

还有一种配置方式就是在工具的配置文件中

```javascript
// babel presets
const presets = [
  [
    "@babel/preset-env",
    {
      "targets": "node < 6",
      "useBuiltIns": "usage",
      "corejs": 3,
      "debug": true
    }
  ]
];
```

***参考：***

- 前端工具配置[实例](https://github.com/browserslist/browserslist-example)
- `browserslist`条件查询[工具](https://github.com/browserslist/browserslist#tools)

***注意：***要不定时进行`browser data`更新，因为我们的配置中可能会用过`> 2%`（使用率），使用`npx browserslist@latest --update-db`命令更新`caniuse-lite`

#### 查询条件

***条件组合***

| 关键字      | 描述                   | 例子                                                         |
| ----------- | ---------------------- | ------------------------------------------------------------ |
| `or`或者`,` | 并集                   | `> .5% or last 2 versions` 、 `> .5%, last 2 versions`       |
| `and`       | 交集                   | `> .5% and last 2 versions`                                  |
| `not`       | 取反（不能放在最前面） | `> .5% and not last 2 versions`、`> .5% or not last 2 versions`、`\> .5%, not last 2 versions` |

***条件说明***

- `> 5%`: 基于全球使用率统计而选择的浏览器版本范围。`>=`,`<`,`<=`同样适用。
- `> 5% in US` : 同上，只是使用地区变为美国。支持两个字母的国家码来指定地区。
- `> 5% in alt-AS` : 同上，只是使用地区变为亚洲所有国家。[这里](https://github.com/ben-eb/caniuse-lite/tree/master/data/regions)列举了所有的地区码。
- `> 5% in my stats` : 使用[定制的浏览器统计数据](https://github.com/browserslist/browserslist#custom-usage-data)。
- `cover 99.5%` : 使用率总和为99.5%的浏览器版本，前提是浏览器提供了使用覆盖率。
- `cover 99.5% in US` : 同上，只是限制了地域，支持两个字母的国家码。
- `cover 99.5% in my stats` :使用[定制的浏览器统计数据](https://github.com/browserslist/browserslist#custom-usage-data)。
- `maintained node versions` :所有还被 node 基金会维护的 node 版本。
- `node 10` and `node 10.4` : 最新的 node 10.x.x 或者10.4.x 版本。
- `current node` :当前被 browserslist 使用的 node 版本。
- `extends browserslist-config-mycompany` :来自browserslist-config-mycompany包的查询设置
- `ie 6-8` : 选择一个浏览器的版本范围。
- `Firefox > 20` : 版本高于20的所有火狐浏览器版本。`>=`,`<`,`<=`同样适用。
- `ios 7` :ios 7自带的浏览器。
- `Firefox ESR` :最新的火狐 ESR（长期支持版） 版本的浏览器。
- `unreleased versions` or `unreleased Chrome versions` : alpha 和 beta 版本。
- `last 2 major versions` or `last 2 ios major versions` :最近的两个发行版，包括所有的次版本号和补丁版本号变更的浏览器版本。
- `since 2015` or `last 2 years` :自某个时间以来更新的版本（也可以写的更具体`since 2015-03`或者`since 2015-03-10`）
- `dead` :通过`last 2 versions`筛选的浏览器版本中，全球使用率低于0.5%并且官方声明不在维护或者事实上已经两年没有再更新的版本。目前符合条件的有 `IE10`,`IE_Mob 10`,`BlackBerry 10`,`BlackBerry 7`,`OperaMobile 12.1`。
- `last 2 versions` :每个浏览器最近的两个版本。
- `last 2 Chrome versions` :chrome 浏览器最近的两个版本。
- `defaults` :默认配置`> 0.5%, last 2 versions, Firefox ESR, not dead`。
- `not ie <= 8` : 浏览器范围的取反。

可以使用`npx browserslist`命令去验证配置条件是否符合预期

#### 浏览器列表

- `Android` for Android WebView.
- `Baidu` for Baidu Browser.
- `BlackBerry` or `bb` for Blackberry browser.
- `Chrome` for Google Chrome.
- `ChromeAndroid` or `and_chr` for Chrome for Android
- `Edge` for Microsoft Edge.
- `Electron` for Electron framework. It will be converted to Chrome version.
- `Explorer` or `ie` for Internet Explorer.
- `ExplorerMobile` or `ie_mob` for Internet Explorer Mobile.
- `Firefox` or `ff` for Mozilla Firefox.
- `FirefoxAndroid` or `and_ff` for Firefox for Android.
- `iOS` or `ios_saf` for iOS Safari.
- `Node` for Node.js.
- `Opera` for Opera.
- `OperaMini` or `op_mini` for Opera Mini.
- `OperaMobile` or `op_mob` for Opera Mobile.
- `QQAndroid` or `and_qq` for QQ Browser for Android.
- `Safari` for desktop Safari.
- `Samsung` for Samsung Internet.
- `UCAndroid` or `and_uc` for UC Browser for Android.
- `kaios` for KaiOS Browser.

***注意：*** 浏览器名称不区分大小写

#### 参考

[browserslist github](https://github.com/browserslist/browserslist)
[Browser list详解](https://eick.gitbook.io/notes/javascript/browser-list-xiang-jie)
