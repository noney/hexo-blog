---
title: 理解css relative
date: 2020-03-10 14:08:57
tags:
     - 日积月累
     - CSS
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

relative表示元素相对定位。

#### relative限制absolute

- 限制定位，absolute位置被限制在relative内
- 限制层级，absolute层级以relative层级为准
- 限制overflow，absolute是不受overflow限制，加上relative，就会被限制。

#### relative限制fixed

- 限制层级，absolute层级以relative层级为准

<!-- more -->

#### relative与定位

- 相对自身
- 无侵入，不会影响其它元素布局
- top/bottom同时存在，bottom不起作用
- left/right同时存在，right不起作用

#### relative与层级

z-index: auto不会限制absolute层级，不包括ie6/ie7，因为ie6/ie7下z-index: auto也会创建层叠上下文。

#### 最小化影响元则

- 尽量避免使用relative，absolute定位不依赖relative

#### 参考

[CSS深入理解之relative](https://www.imooc.com/learn/565)

