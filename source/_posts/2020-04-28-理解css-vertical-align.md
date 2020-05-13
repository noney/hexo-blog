---
title: 理解css-vertical-align
date: 2020-04-28 10:31:07
tags:
     - 日积月累
     - CSS
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

vertical-align 属性设置元素的垂直对齐方式。

**vertical-align属性值**

- inherit 继成
- 线类 包括`baseline（默认值）、top、middle、bottom`
- 文本类 包括`text-top、text-bottom`
- 上标下标类 包括`sup、super`
- 数值百分比类（支持负值）  如`20%、20px、20em`

数值百分比类属性，就是在`baseline`基础上进行数值大小的上下偏移

百分比值是相对于`line-height`来计算的

<!-- more -->

**vertical-align起作用前提**

*inline水平*

- inline 包括`span、img、strong、em`等
- Inline-block 包括`input、button`等，在IE8+里面是`inline-block`，IE6/7则为`inline`

*table-cell*

- table-cell 包括 `td`，作用于自身

**vertical-align和line-height**

如果`inline-block`元素