---
title: 理解css-line-height
date: 2020-04-27 10:58:37
tags:
     - 日积月累
     - CSS
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

line-height属性设置行间的距离（两行文字基线之前的距离，不允许使用负值），只对`非替换inline元素`起作用。

```html
<!-- line-height具有继成特性，在这里里面inline元素aaa继成了父元素的行高 -->
<div class="lh1">aaa</div>
<span class="lh2">bbb</span>
<em class="lh3">ccc</em>
<!-- 替换元素不起作用 -->
<img src="test.jpg" alt="" class="lh4">
<!-- 块元素不起作用 -->
<div class="lh5"></div>
```

<!-- more -->

```css
.lh1, .lh2, .lh3, .lh4, .lh5 {
    line-height: 200px;
    background-color: red;
}
.lh2 {
    background-color: green;
}
.lh3 {
    background-color: blue;
    display: inline-block;
}
.lh4 {
    background-color: black;
}
.lh5 {
    background-color: purple;
}
```

**line-height与行内框盒子模型**

*行内框盒子模型*

```html
<p>这是一行普通的文字，这里有个<em>em</em>标签。</p>
```

- 内容区域盒子，是一种围绕文字看不见的盒子，大小与`font-szie`有关
- 内联盒子（inline boc），它的作用是让内容在一行显示 ，外面由内联标签包裹的称为"内联盒子"（如上在的em标签），如果只是文字的话，称为"匿名内联盒子"。
- 行框盒子（line box），每一行就是一个行框盒子，它由一个个的内联盒子组成。
- 包含盒子（containing box），如上面的`p`标签就是一个包含盒子，它由一个个行框盒子组成。

**line-height高度原理**

*内联元素的高度是由`line-height`决定的*

*内容区域高度 + 行间距 = 行高 = 高度*

- 内容区域高度与字号及字体有关，与line-height无关
- 在宋体字体下，内容区域高度等于字体大小值 （font-size + 行间距 = line-height）

***总结：*** 行高决定内联元素的高度，行间距可大可小（甚至为负值），来保证高度正好等于行高

***理解：*** 高度是由内容区域和行间距决定的，行高决定了行间距

**line-height属性值**

- normal 默认值，由浏览器决定且写字体有关
- <number> 数值作为行高，根据当前元素的`font-size`大小来计算的
- <length> 具体长度值，如`px pt rem em`
- <precent>百分比作为行高，相对于设置了该`line-height`属性的元素的`font-size`大小计算
- inherit 继承，IE8+

*line-height: 1.5、line-height: 150%、line-height: 1.5em区别*

```html
<!-- p元素的行高会根据父元素的行高，自行计算 1.5 * 60 = 90 -->
<div class="number">
    <p>中华人民共和国</p>
    <p>中华人民共和国</p>
</div>
<!-- p元素的行高由父元素的行高决定 1.5 * 24 = 36 -->
<div class="precent">
    <p>中华人民共和国</p>
    <p>中华人民共和国</p>
</div>
<!-- p元素的行高由父元素的行高决定 1.5 * 24 = 36 -->
<div class="len">
    <p>中华人民共和国</p>
    <p>中华人民共和国</p>
    <p>中华人民共和国</p>
</div>
```

```css
div {
    font-size: 24px;
}
div p {
    font-size: 60px;
}
.number {
    line-height: 1.5;
}
.precent {
    line-height: 150%;
}
.len {
    line-height: 1.5em;
}
```

**line-height与图片**

*在block元素中，会存在空白幽灵节点*

- 使用`text-align:center`时，图片前面存在空白幽灵节点

```html
<div class="lh">
    <img src="test.jpg" alt="">
</div>
```

```css
.lh {
    background-color: #000;
    text-align: center;
}
span {
    background-color: red;
}
```

- 图片底线是和空白幽灵节点的基线对齐，由于空白节点存在，所以图片距离底边距的距离就*半行距*（`line-height`作用的对象是空白幽灵节点，和图片没有关系，图片是替换元素，不会受`line-height`影响）

```html
<div class="lh1">
    <span>xxxxxx</span>
    <img src="test.jpg" alt="">
    <span>xxxxxxx</span>
</div>
```

```css
.lh1 {
    background-color: blue;
    line-height: 800px
}
span {
    background-color: green;
    display: inline-block;
}
```



```html
<div class="lh">
    <img src="test.jpg" alt="">
    <span>spxce</span>
</div>
```

```css
.lh {
    background-color: #000;
    color: white;
    line-height: 90px;
}
span {
    background-color: red;
}
```

*修复方法*

- 图片块状化`display: block` ，`vertical-align`属性只适用于`inline、inline-block`元素
- 图片底线对齐`vertical-align: bottom`
- 父元素`line-height: 0`

**line-height应用**

- 图片水平垂直居中 IE8+

```html
<div class="box">
    <img src="test.jpg" alt="">
</div>
```

```css
.box {
    background-color: #000;
    line-height: 800px;
    text-align: center;
}
.box img {
    vertical-align: middle;
}
```

- 多行文本水平垂直居中 IE8+

```html
<div class="box">
    <div class="inner">
        <p>中华人民共和国</p>
        <p>中华人民共和国</p>
        <p>中华人民共和国</p>
    </div>
</div>
```

```css
.box {
    background-color: #000;
    line-height: 800px;
    text-align: center;
    color: white;
}
.box .inner {
    vertical-align: middle;
    line-height: normal;
    display: inline-block;
}
```

**参考**

[CSS深入理解之line-height](https://www.imooc.com/learn/403)

