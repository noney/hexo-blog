---
title: sass文档-数据类型-colors
date: 2020-06-09 10:56:11
tags:
  - 日积月累
  - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`sass`内置了对`color`的支持

```scss
@debug #f2ece4; // #f2ece4
@debug #b37399aa; // rgba(179, 115, 153, 67%)
@debug midnightblue; // #191970
@debug rgb(204, 102, 153); // #c69
@debug rgba(107, 113, 127, 0.8); // rgba(107, 113, 127, 0.8)
@debug hsl(228, 7%, 86%); // #dadbdf
@debug hsla(20, 20%, 85%, 0.7); // rgb(225, 215, 210, 0.7)
```

<!-- more -->

#### 内置函数

`sass`内置了许多操作颜色的函数，通过这些函数可以从基础的颜色`创造`出新的颜色

```scss
$venus: #998099;

@debug scale-color($venus, $lightness: +15%); // #a893a8
@debug mix($venus, midnightblue); // #594d85
```

#### 参考

[Colors](https://sass-lang.com/documentation/values/colors)