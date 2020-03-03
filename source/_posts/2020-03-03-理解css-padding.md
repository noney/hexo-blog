---
title: 理解css-padding
date: 2020-03-03 10:26:50
tags:
     - 日积月累
     - CSS
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://noney.coding.me/)*

__作者：[Noney Li]__

#### 写在前面

padding是盒模形中的内边距。中规中矩，兼容性很好。

#### padding与容器的尺寸

*block元素*

| 宽度值固定     | 宽度值为auto     | box-sizing 值为 border-box                     |
| -------------- | ---------------- | ---------------------------------------------- |
| 会影响元素宽度 | 不会影响元素宽度 | 不会影响元素宽度 **padding值大于宽度时会影响** |

高度值无论为什么，都会影响元素高度。

<!-- more -->

*inline元素*

会影响元素宽度，不会影响元素高度（会占据空间，加背影色验证）。

*利用inline元素，实现分隔符*

```html
<div>注册<span class="line"></span>退出登录</div>
```

```css
.line {
    border-left: 2px solid;
    font-size: 0;
    padding: 13px 5px 1px;
    margin-left: 12px;
}
```

#### padding负值和百分比值

*padding不支持负值*

*padding百分笔值是根据***父元素**宽度计算的*

*inline元素同样的padding，高度会大于宽度（设置font-size: 0会解决这个问题，行内元素的空白节点所置），会断行*

#### 标签元素的内置padding

#### padding与图形绘制

*实现三道杠*

```html
<div class="line-tri"></div>
```

```css
.line-tri {
    width: 150px;
    height: 30px;
    padding: 15px 0;
    border-top: 30px solid;
    border-bottom: 30px solid;
    color: #000;
    background-color: currentColor;
    background-clip: content-box;
}
```

*白眼效果*

```html
<div class="eye"></div>
```

```css
.eye {
    width: 100px;
    height: 100px;
    border-radius: 50%;
    color: #000;
    padding: 10px;
    background-color: currentColor;
    background-clip: content-box;
    border: 10px solid;
}
```

#### padding与布局

*两栏自适应布局*

```html
<div class="box">
    <img src="mm.jpg" alt="">
    <div class="auto">中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来。</div>
</div>
```

```css
img {

    float: left;
}
.auto {
    padding-left: 150px;
}
```

```html
<div class="box">
    <img src="mm.jpg" alt="">
    中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来。
</div>
```

```css
.box {
    padding-left: 150px;
}
img {
    float: left;
    margin-left: -120px;
}
.auto {
    padding-left: 150px;
}
```

#### 参考

[CSS深入理解之padding](https://www.imooc.com/learn/710)