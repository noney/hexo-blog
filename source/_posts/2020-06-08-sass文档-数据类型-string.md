---
title: sass文档-数据类型-string
date: 2020-06-08 16:56:30
tags:
    - 日积月累
    - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`sass`支持带引号的字符串和不带引号的字符串。

- `string.unquote()`函数，将带引号的字符串转换为不带引号的字符串
- `string.quote()`函数，将不带引号的字符串转换为带引号的字符串

```scss
@use "sass:string";

@debug string.unquote(".widget:hover"); // .widget:hover
@debug string.quote(bold); // "bold"
```
<!-- more -->

#### 转义

字符串可以使用`\`进行转义，它后面可以跟`unicode`字符

```scss
@debug "\""; // '"'
@debug \.widget; // \.widget
@debug "\a"; // "\a" (a string containing only a newline)
@debug "line1\a line2"; // "line1\a line2"
@debug "Nat + Liz \1F46D"; // "Nat + Liz 👭"
```


#### 引用

字符串被`""或''`包裹，如果想在双引号中插入单引号或者在单引号中插入双引号，需要使用`\`进行转义

```scss
@debug "Helvetica Neue"; // "Helvetica Neue"
@debug "C:\\Program Files"; // "C:\\Program Files"
@debug "\"Don't Fear the Reaper\""; // "\"Don't Fear the Reaper\""
@debug "line1\a line2"; // "line1\a line2"

$roboto-variant: "Mono";
@debug "Roboto #{$roboto-variant}"; // "Roboto Mono"
```

#### 非引用

不带引号的字符串，被认为是标识符

```scss
@debug bold; // bold
@debug -webkit-flex; // -webkit-flex
@debug --123; // --123

$prefix: ms;
@debug -#{$prefix}-flex; // -ms-flex
```

***注意：***不是所有不带引号的字符串，都被解析成标识符，为了不会使用错误，字符串尽量带引号

- `css`颜色名字被解析成颜色 `red`
- `null`被解析成`sass`空值
- `true、false`被解析成布尔值
- `not、and、or`被解析成布尔操作符

当解析无引号字符串时，转义的文本将作为字符串的一部分进行解析

- 如果它是一个有效的标识符，返回引用（加引号）的字符串  `@debug \1F46D; // 👭`
- 如果它是一个可打印的字符，则将其包含在`\`之后 `@debug \21; // \!`
- 否则，小写Unicode转义将包含在尾随空格中 `\7Fx; // \7f x`

#### 字符串索引

字符串索引从`0`开始，负数从字符串末尾返回

```scss
@use "sass:string";

@debug string.index("Helvetica Neue", "Helvetica"); // 1
@debug string.index("Helvetica Neue", "Neue"); // 11
@debug string.slice("Roboto Mono", -4); // "Mono"
```

#### 参考

[Strings](https://sass-lang.com/documentation/values/strings)