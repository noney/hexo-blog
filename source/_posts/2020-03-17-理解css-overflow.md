---
title: 理解css overflow
date: 2020-03-17 14:44:04
tags:
     - 日积月累
     - CSS
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

overflow属性规定当内容溢出元素框时的表现。

#### overflow基本属性

- visible 默认
- hidden
- scroll
- auto
- inherit

*css3 overflow-x 和 overlfow-y 支持IE8+*

- 两个值相同时，等同于overflow
- 当一个值为visible，另一个值为hidden、scroll、auto时，visible会被重置为auto

<!-- more -->

*兼容性*

- 宽度设定机制

在IE7下，当宽度为100%时，当出现竖向滚动条，浏览器会将滚动条宽度算入横向宽度内，这样横向的宽度就不会满100%，会出现横向滚动条，解决方法是去掉宽度100%的值。

*overflow生效条件*

- 非display: inline水平
- 对应方位的尺寸限制。width/height/max-width/max-height/absolute拉伸
- 对于td，需要设置table table-layout: fixed

*小技巧*

在IE7下，使用overflow: visible去除button两边的多余留白

IOS下，使用webkit-overflow-scrolling: touch体验原生滚动条

#### overflow与滚动条

*html/body与滚动条*

- 所有浏览器的默认滚动条都来自`<html>`，而不是`<body>`
- html默认值

| IE6/7                     | ie8+                  | 备注                                                         |
| ------------------------- | --------------------- | ------------------------------------------------------------ |
| html {overlfow-y: scroll} | html {overflow: auto} | IE6/7只是表现类似，一直有竖向滚动条，可以使用html {overflow: hidden}去掉它 |

- 滚动高度

| Chrome                  | 其它                               |
| ----------------------- | ---------------------------------- |
| document.body.scrollTop | document.documentElement.scrollTop |

*padding-bottom缺失，chrome以外所有浏览器都会出现此问题*

*宽度机制*

- 滚动条会占用容器的可用宽度和高度

#### overflow与BFC

*触发属性*

- auto
- scroll
- Hidden

*内部浮动无影响 支持IE7+*

```html
<div class="box">
  <img src="img.jpg" alt="">
</div>
```

```css
.box {
  background-color: blue;
  overflow: hidden;
}
.box img {
  float: left;
}
```



*避免margin穿透*

*两栏自适应布局*

```html
<div class="box">
  <img src="img.jpg" alt="" class="left">
  <div class="right">
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
    <p>中国的中国的中国的中国的中国的中国的中国的中国的</p>
  </div>
</div>
```

```css
.left {
  float: left;
  width: 100px;
  height: 100px;
  margin-right: 22px;
}
.right {
  overflow: hidden;
}
```

*其它BFC属性表现*

- overflow: hidden 自适应，它的**溢出不可见**特性，限制了它的应用场景
- float + float 有包裹性和破坏性，无限自适应，一般用在块状浮动布局
- position: absolute 脱离文档流，自娱自乐
- display: inline-block 包裹性，无法自适应，IE6/7无法识别它，不存在包裹性
- display: table-cell 包裹性，但天生无溢出特性（宽度永远不会超出父容器），也能实现自适应

综上所述，我们能使用的只有overflow: hidden、display: inline-block、display: table-cell，可以实现两栏自适应布局

```css
/*副作用明显，会让容器外面的普通元素不可见*/
.cell {
  overflow: hidden;
  _display: inline-block;
}
/*最佳实现*/
.cell {
  /*IE8+ BFC特性*/
  display: table-cell;
  width: 2000px;
  /* IE7 伪BFC特性*/
  *display: inline-block;
  *width: auto;
}
```

#### overflow与绝对定位

*隐藏失效*

```html
<div class="box">
  <img src="img.jpg" alt="">
</div>
```

```css
.box {
  width: 200px;
  height: 300px;
  overflow: hidden;
}
img {
  position: absolute;
}
```

*滚动失效*

```html
<div class="box">
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <img src="img.jpg" alt="">
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
  <p>中国的中国的中国的中国的中国的中国的中国的</p>
</div>
```

```css
.box {
  width: 900px;
  height: 200px;
  border: 1px solid blue;
  overflow: auto;
}
img {
  position: absolute;
}
```

***失效原因：当overflow在绝对定位元素及其包含块（含relative/absolute/fixed声明的父级元素，没有则body元素）之间的时候，绝对定位元素不会被裁剪***

*避免失效*

- overflow元素自身为包含块

```html
<div class="box">
  <img src="img.jpg" alt="">
</div>
```

```css
.box {
  width: 200px;
  height: 300px;
  overflow: hidden;
  position: relative;
}
img {
  position: absolute;
}
```

- overflow元素的子元素为包含块

```html
<div class="box">
  <div class="warp">
    <img src="img.jpg" alt="">
  </div>
</div>
```

```css
.box {
  width: 900px;
  height: 200px;
  border: 1px solid blue;
  overflow: auto;
}
.warp {
  position: relative;
}
img {
  position: absolute;
}
```



- 任意合法transform声明为包含块

> overflow自身添加transform IE9+/FireFox生效，其它浏览器不生效
>
> overflow子元素添加transform 所有浏览器生效 webkit浏览器动态渲染异常

```html
<div class="box">
  <div class="warp">
    <img src="img.jpg" alt="">
  </div>
</div>
```

```css
.box {
  width: 900px;
  height: 200px;
  border: 1px solid blue;
  overflow: auto;
}
.warp {
  transform: rotate(0);
}
img {
  position: absolute;
}
```

*overflow失效妙用*

```html
<div class="warp">
  <div class="con">
  </div>
  <div class="box">
    <ul>
      <li><a href="">1</a></li>
      <li><a href="">2</a></li>
      <li><a href="">3</a></li>
    </ul>
  </div>
</div>
```

```css
html, body {
  height: 100%;
  overflow: scroll;
}
.warp {
  width: 1200px;
  margin: 0 auto;
  height: 100%;
}
.con {
  float: left;
  width: 100%;
  border: 1px solid #999;
  border-top-color: transparent;
  border-bottom-color: transparent;
  height: 120%;
  background-color: #ccc;
}
.box {
  display: inline-block;
  position: absolute;
  text-align: right;
  margin-left: 10px;
  bottom: 30px;
}
.box li {
  background-color: #ccc;
  list-style-type: none;
  border-radius: 100%;
  margin-bottom: 10px;
  width: 30px;
  height: 30px;
  line-height: 30px;
  text-align: center;
}
.box li a {
  color: #fff;
  text-decoration: none;
}
```

**参考**

[CSS深入理解之overflow](https://www.imooc.com/learn/256)