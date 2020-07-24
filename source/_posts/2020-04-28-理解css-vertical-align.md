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

`inline-block`元素的`基线`是正常流中最后一个`line box`的`基线`，如果`line box`里面没有`line boxes`（内容为空）或者`overflow`属性值不为`visible`，它的基线是`margin`底边缘

- `overflow`属性值不为`visible`

```html
  <div class="va">
    <p class="box one">x</p>
    <p class="box">x-baseline</p>
  </div>
```

```css
    .box {
      display: inline-block;
      width: 100px;
      height: 100px;
      background-color: blue;
      color: #fff;
    }
    .one {
      overflow: auto;
    }
```

- 内容为空

```html
  <div class="va">
    <p class="box one"></p>
    <p class="box">x-baseline</p>
  </div>
```

```css
    .box {
      display: inline-block;
      width: 100px;
      height: 100px;
      background-color: blue;
      color: #fff;
    }
```

#### 线类属性值

*bottom*

- inline / inline-block 当前元素底部和整行（所有内联元素）的底部对齐（父元素的底部对齐）
- table-cell 单元格底`padding`边缘和表格行的底部对齐

*top*

- inline / inline-block 当前元素底部和整行的顶部对齐（父元素的顶部对齐）
- table-cell 单元格底`padding`边缘和表格行的顶部对齐

*middle*

- inline / inline-block 当前元素`中心点`和父元素`基线`上`1/2 x-height`处对齐
- table-cell 单元格填充盒子相对于外面的表格行居中对齐

#### 文本类属性

***下面`demo`使用结构如下：***

```html
<div class="va"><span class="txt before">x</span><img src="test.jpg" alt=""><span class="txt">x</span></div>
```

*text-top* 当前元素的项部和父级`content area`项部对齐

*text-bottom* 当前元素的底部和父级`content area`底部对齐

```css
    .va {
      line-height: 30;
      background-color: #ccc;
      font-size: 30px; //父级的font-size
    }
    img {
      vertical-align: text-top;
    }
    .txt {
      display: inline-block;
      background-color: blue;
      line-height: normal;
    }
    .before {
      font-size: 20px;
    }
```

- 前后的元素都没有关系

```css
    .va {
      line-height: 30;
      background-color: #ccc;
      font-size: 30px; //父级的font-size
    }
    img {
      vertical-align: text-top;
    }
    .txt {
      display: inline-block;
      background-color: blue;
      line-height: normal;
    vertical-align: top; // 前后元素都设置的上部对齐，图片对齐位置不变（和一个看不见的继成父级字体大小的元素对齐）
    }
    .before {
      font-size: 20px;
    }
```

- 与`line-height`没有关系，只于父级的`font-size`有关

```css
    .va {
      line-height: 10; // 行高改变，图片对齐位置不变
      background-color: #ccc;
      font-size: 30px;
    }
    img {
      vertical-align: text-top;
    }
    .txt {
      display: inline-block;
      background-color: blue;
      line-height: normal;
    }
    .before {
      font-size: 20px;
    }
```

***文字与图标对齐***

- aseline对齐`图标偏上`
- top/bottom对齐会受其它元素影响（相对于整行对齐，所有内联元素）
- middle需要恰好的字体大小，兼容性不高（ie6/7）
- text-top/text-bottom比较合适，不受`其它元素`及`行高`的影响

#### 上标下标类

*super* 提高当前元素的基线到父级合适的上标基线位置

*sub* 降低当前元素的基线到父级合适的下标基线位置

#### 前后值不一的表现

***前后`inline`元素的`vertical-align`值是相互独立的，不会有任何相互影响***

*top/bottom*

```html
<div class="va"><span class="txt before">x</span><img src="test.jpg" alt=""><span class="txt">x</span></div>
```

```css
    .va {
      line-height: 10;
      background-color: #ccc;
      font-size: 30px;
    }
    img {
      vertical-align: top; // 替换元素默认是底边与基线对齐，`top`相对于父元素顶部对齐，此时出现的文字移到项部的现象，并不是图片顶部对齐带来的影响（前后不会有任何影响），是因为图片下沉了，`dom`元素默认`左上`对齐导致的
    }
    .txt {
      display: inline-block;
      background-color: blue;
      vertical-align: bottom; // 此时文字相对于父元素底部对齐，也证实了前后不会有任何影响
    }
```

*text-top/text-bottom*

```html
<div class="va"><span class="txt before">我是bottom对齐</span><img src="test.jpg" alt=""><span class="txt">我是参照物</span></div>
```

```css
    .va {
      line-height: 30;
      background-color: #ccc;
      font-size: 30px;
    }
    img {
      vertical-align: text-top; // 相对于父级`content area`顶部对齐
    }
    .txt {
      display: inline-block;
      background-color: blue;
    }
    .before {
      vertical-align: text-bottom; // 相对于父级`content area`底部对齐
    }
```

#### 近似垂直居中

`vertical-align: middle`并不会完全居中，因为字体都会有下沉特性

- `font-size: 0`，消除文字影响，缺点：会导致里面字体不见

```html
  <div class="va">
    <img src="test.jpg" alt="">
    <span class="txt">我是参照物</span>
  </div>
```

```css
    .va {
      line-height: 50;
      background-color: #ccc;
    }
    img {
      vertical-align: middle;
    }
    .txt {
      font-size: 0;
      display: inline-block;
      background-color: blue;
    }
```

- 添加空白结点

```html
  <div class="va">
    <span class="empty"></span>
    <img src="test.jpg" alt="">
    <span class="txt">我是参照物</span>
  </div>
```

```css
    .va {
      line-height: 50;
      background-color: #ccc;
    }
    img {
      vertical-align: middle;
    }
    .empty {
      display: inline-block;
      vertical-align: middle; // 写图片的`middle`合二为一，同时向下偏移，根据`dom`元素默认`左上`对齐，两个元素同时上移
    }
    .txt {
      display: inline-block;
      background-color: blue;
    }
```

***总结：***应用了`vertical-align`属性的元素，只跟`父级`发生关系，跟前后元素没有直接影响

#### 兼容性

- `ie6/7`图文一体，解决办法后面元素设置`display: inline-block`
- `ie6/7middle`浏览器解析问题

#### 实践应用

- 小图标与文字对齐，使用`vertical-align`负值，没有兼容性问题
- 不定尺寸图片

```html
  <div class="va">
    <img src="test.jpg" alt="">
    <i class="assist"></i>
  </div>
```

```css
    .va {
      height: 800px;
      background-color: #ccc;
    }
    /*辅助元素-基线对齐*/
    .assist {
      display: inline-block;
      height: 100%;
      vertical-align: middle;
    }
    img {
      vertical-align: middle;
    }
```

- 多行文字垂直居中

```html
  <div class="va">
    <div class="box">
      <p>我第一行</p>
      <p>我第二行</p>
      <p>我第三行</p>
    </div>
    <i class="assist"></i>
  </div>
```

```css
    .va {
      height: 300px;
      background-color: #ccc;
      text-align: center;
    }
    /*辅助元素-基线对齐*/
    .assist {
      display: inline-block;
      height: 100%;
      vertical-align: middle;
    }
    /*主体元素inline-block化*/
    .box {
      display: inline-block;
      vertical-align: middle;
    }
```

#### 参考

[CSS深入理解之vertical-align](https://www.imooc.com/learn/542)