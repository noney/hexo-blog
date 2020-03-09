---
title: 理解css z-index
date: 2020-03-05 14:40:12
tags:
     - 日积月累
     - CSS
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://noney.coding.me/)*

__作者：[Noney Li]__

#### 写在前面

z-index表示元素的层叠顺序。

#### z-index基础

*属性值*

- z-index: auto（默认值）
- z-index: <integer> 整数值
- z-index: inherit 继承

*特性*

- 支持负值
- 支持css animation动画
- 在css2.1时代，需要和定位元素配合使用

<!-- more -->

#### z-index与定位元素

*无嵌套情况*

- 后来者居上
- 哪个z-index值大，哪个在上面

嵌套情况

- 祖先优先原则，前提是z-index的值是数值，非auto

#### 层叠上下文和层叠基础

*层叠上下文，是元素在Z轴上的表现*

- 页面根元素天生具有层叠上下文
- z-index值为数值的定位元素也具有层叠上下文
- 其它属性

*层叠水平*

- 决定了同一个层叠上下文中元素在Z轴上的显示顺序
- 层叠水平不等于z-index，普通元素也有层叠水平

#### 层叠顺序

*7阶层叠水平*

- 层叠上下文background/order
- 负z-index
- block块状水平盒子
- float浮动盒子
- Inline/inline-block水平盒子
- z-index: auto或看成z-index: 0，不依赖z-index的层叠上下文
- 正z-index

#### z-index与层叠上下文

- 定位元素默认z-index: auto，可以看成z-index: 0

```html
<img src="img.jpg" alt="" class="z1">
<img src="img.jpg" alt="" class="z2">
```

```css
.z1 {
    position: relative;
}
.z2 {
    margin-left: -30px;
}
```

- z-index值不为auto的定位元素会创建层叠上下文

```html
<div class="box">
    <img src="img.jpg" alt="" class="z1">
</div>
```

```css
.box {
    position: absolute;
    background-color: blue;
    z-index: 0;
}
img {
    position: relative;
    margin-left: -100px;
    z-index: -1;
}
```



- Z-index层叠顺序受限于层叠上下文

```html
<div class="box1">
    <img src="img.jpg" alt="">
</div>
<div class="box2">
    <img src="img.jpg" alt="">
</div>
```

```css
.box1 {
    position: relative;
    z-index: 1;
}
.box1 img {
    position: absolute;
    z-index: 2;
}
.box2 {
    position: relative;
    z-index: 0;
    margin-left: 200px;
}
.box2 img {
    position: absolute;
    z-index: 9999999;
}
```

#### 其它css属性与层叠上下文

- 父元素为display: flex或inline-flex，子元素z-index值不为auto

```html
<div class="box">
    <div>
        <img src="img.jpg" alt="">
    </div>
</div>
```

```css
.box {
    display: flex;
}
.box > div {
    z-index: 1;
    background-color: blue;
}
.box > div > img {
    z-index: -1;
    position: relative;
}
```

- opacity不等于1与层叠上下文

```html
<div class="box">
    <img src="img.jpg" alt="">
</div>
```

```css
.box {
    opacity: 0.5;
    background-color: blue;
}
.box > img {
    z-index: -1;
    position: relative;
}
```

- transform不等于none与层叠上下文

```html
<div class="box">
    <img src="img.jpg" alt="">
</div>
```

```css
.box {
    transform: rotate(0);
    background-color: blue;
}
.box > img {
    z-index: -1;
    position: relative;
}
```

- Mix-blend-mode不等于normal与层叠上下文

```html
<div class="box">
    <img src="img.jpg" alt="">
</div>
```

```css
.box {
    mix-blend-mode: screen;
    background-color: blue;
}
.box > img {
    z-index: -1;
    position: relative;
}
```

- filter与层叠上下文

```html
<div class="box">
    <img src="img.jpg" alt="">
</div>
```

```css
.box {
    filter: blur(5px);
    background-color: blue;
}
.box > img {
    z-index: -1;
    position: relative;
}
```

- isolation: idsolate与层叠上下文

```html
<div class="box">
    <img src="img.jpg" alt="">
</div>
```

```css
.box {
    isolation: idsolate;
    background-color: blue;
}
.box > img {
    z-index: -1;
    position: relative;
}
```

- position: fixed与层叠上下文（不需要z-index值辅助）

```html
<div class="box">
    <img src="img.jpg" alt="">
</div>
```

```css
.box {
    position: fixed;
    background-color: blue;
}
.box > img {
    z-index: -1;
    position: relative;
}
```

- will-change与层叠上下文

```html
<div class="box">
    <img src="img.jpg" alt="">
</div>
```

```css
.box {
    will-change: transform;
    background-color: blue;
}
.box > img {
    z-index: -1;
    position: relative;
}
```

- Webkit-overflow-scrolling与层叠上下文

#### z-index与其它css属性层叠上下文

- 不依赖z-index值创建层叠上下文的层叠顺序均是z-index: auto级别

- 依赖z-index值创建层叠上下文的层叠顺序取决于z-index值

> position值为relative/absolute或fixed（部分浏览器）
>
> Display: flex|inline-flex的子flex项


```html
<img src="img.jpg" alt="" class="a">
<div class="box">
    <img src="img.jpg" alt="">
</div>
```

```css
img.a {
    position: absolute;
    z-index: 1;
    margin-left: 100px;
}
.box {
    display: flex;
    background-color: blue;
}
.box > img {
    z-index: 1;
}
```

#### 参考

[CSS深入理解之z-index](https://www.imooc.com/learn/643)

