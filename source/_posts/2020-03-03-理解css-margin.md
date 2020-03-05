---
title: 理解css-margin
date: 2020-03-03 17:13:09
tags:
     - 日积月累
     - CSS
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://noney.coding.me/)*

__作者：[Noney Li]__

#### 写在前面

margin是盒模型中的外边框。

#### margin与元素的尺寸

- 可视尺寸 clientWidth：content + padding + border

- 占据尺寸 outerWidth：content + padding + border + margin

- 对于可视尺寸影响的条件：没有设置宽高的普通block元素*，并且只会影响*水平方向的尺寸

*一侧定宽自适应布局*

```html
<div class="box">
    <img src="" alt="" width="150">
    <p>中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来。</p>
    
</div>
```

<!-- more -->

```css
.box {
    width: 500px;
}
img {
    float: left;
    width: 150px;
    height: 150px;
}
p {
    margin-left: 150px;
}
```

- 对于占据尺寸影响的条件： block/inline元素均适用、无论*是否设置宽高*、无论水平还是垂直方向均适用

*滚动窗口上下留白*

```html
<div class="box">
    <p>中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来，中国的等待秋去冬来。</p>
    
</div>
```

```css
.box {
    width: 200px;
    height: 200px;
    overflow: scroll;
}
p {
    margin: 10px 0;
}
```

#### margin与百分比

| 元素定位方式       | 计算方式                 |
| ------------------ | ------------------------ |
| 普通元素百分比     | 父元素容器宽度           |
| 绝对定位元素百分比 | 第一个定位属性的祖先元素 |

*2:1矩形*

```html
<div class="box">
    <div class="mar"></div>
</div>
```

```css
.box {
    width: 400px;
    height: 250px;
    background-color: olive;
    overflow: hidden;
}
.mar {
    margin: 50%;
}
```

#### margin重叠

*重叠特性*

- 重叠只会发生在block元素（不包括float和absolute元素）。
- 不考虑writing-mode，只发生在垂直方向。

*重叠情境*

- 相邻的兄弟元素

```html
<div class="box">
    <p>aaa</p>
    <p>aaa</p>
</div>
```

```css
p {
    background-color: #000;
    margin: 10px;
    color: #fff;
}
```

- 父级和第一个/最后一个元素

| margin-top重叠条件                   | margin-bottom重叠条件                        |
| ------------------------------------ | -------------------------------------------- |
| 父元素非块状格式化上下文元素（BFC）  | 同左                                         |
| 父元素没有border-top设置             | 同左                                         |
| 父元素没有padding-top设置            | 同左                                         |
| 父元素和子元素之间没有inline元素分隔 | 同左                                         |
| -                                    | 父元素没有height、min-height、max-height限制 |

```html
<div class="father">
    <div class="son" style="margin-top: 80px;">son</div>
</div>
<!-- 等同于 -->
<div class="father" style="margin-top: 80px;">
    <div class="son">son</div>
</div>
<!-- 等同于 -->
<div class="father" style="margin-top: 80px;">
    <div class="son" style="margin-top: 80px;">son</div>
</div>
```

```css
.father {
    background-color: #f0f3f9;
}
.son {
    margin-top: 80px;
}
```

- 空的block元素

| 触发条件                   |
| -------------------------- |
| 没有border设置             |
| 没有padding设置            |
| 里面没有inline元素         |
| 没有height、min-height设置 |

```html
<div class="father">
    <div class="son"></div>
</div>
```

```css
.father {
    background-color: #f0f3f9;
    overflow: hidden;
}
.son {
    margin: 20px;
}
```

*计算规则*

- 正正取大值

```html
<p class="a">a</p>
<p class="b">b</p>
```

```css
.a {
    margin-bottom: 20px;
}
.b {
    margin-top: 10px;
}
```

```html
<div class="father">
    <div class="son">son</div>
</div>
```

```css
.father {
    margin-top: 20px;
}
.son {
    margin-top: 10px;
}
```

```html
<div class="father">
    <div class="son"></div>
</div>
```

```css
.father {
    background-color: #000;
    overflow: hidden;
}
.son {
    margin-top: 20px;
    margin-bottom: 30px;
}
```

- 正负值相加

```html
<p class="a">a</p>
<p class="b">b</p>
```

```css
.a {
    margin-bottom: 20px;
}
.b {
    margin-top: -10px;
}
```

```html
<div class="father">
    <div class="son">son</div>
</div>
```

```css
.father {
    margin-top: 20px;
}
.son {
    margin-top: -10px;
}
```

```html
<div class="father">
    <div class="son"></div>
</div>
```

```css
.father {
    background-color: #000;
    overflow: hidden;
}
.son {
    margin-top: 20px;
    margin-bottom: -30px;
}
```

- 负负最负值

```html
<p class="a">a</p>
<p class="b">b</p>
```

```css
.a {
    margin-bottom: -20px;
}
.b {
    margin-top: -10px;
}
```

```html
<div class="father">
    <div class="son">son</div>
</div>
```

```css
.father {
    margin-top: -20px;
}
.son {
    margin-top: -10px;
}
```

```html
<div class="father">
    <div class="son"></div>
</div>
```

```css
.father {
    background-color: #000;
    overflow: hidden;
}
.son {
    margin-top: -20px;
    margin-bottom: -30px;
}
```

#### margin auto

**用于分配剩余空间，前提条件是元素没有设置宽度时候，它会自动充满容器，设置宽度以后，就会出现剩余空间。**

*一侧定值，一侧auto，auto就是剩余空间的大小*

```html
<div class="father">
    <div class="son">son</div>
</div>
```

```css
.son {
    background-color: #000;
    width: 500px;
    margin-right: 100px;
    margin-left: auto;
}
```

*两侧均为auto，则平分剩余空间*

```html
<div class="father">
    <div class="son">son</div>
</div>
```

```css
.son {
    background-color: #000;
    width: 500px;
    margin-right: auto;
    margin-left: auto;
}
```

*图片为何不居中，图片即使不设置宽度的时候，它也不会充满容器，所以它不会产生剩余空间，我们所要设置图片display: block它才会满足auto条件*

```html
<div class="father">
    <img src="" alt="" width="300">
</div>
```

```css
img {
    margin: 0 auto;
    height: 300px;
    border: 1px solid red;
    display: block;
}
```

*垂直为何不居中，在不设置高度的情况下，它不会充满容器，也就不会产生剩余空间，不能满足auto分配剩余空间的条件*

*absolute + margin实现垂直居中，当元素定位为absolute时，并且设置方向值全为0时，就会触发在不设置宽高时充满容 器条件，此时我们再设置宽高，就会产生剩余空间，此时已满足auto的条件*

```html
<div class="father">
    <div class="son">son</div>
</div>
```

```css
.father {
    position: relative;
    height: 200px;
}
.son {
    position: absolute;
    background-color: #000;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    width: 200px;
    height: 100px;
    margin: auto;
}
```

#### margin负值定位

*负值两端对齐*

如果容器没有设置宽度，margin正直可以使宽度变小，负值可以使宽度变大，float元素除外。

```html
<div class="box">
   <div class="ul">
       <div class="li">aaa</div>
       <div class="li">aaa</div>
       <div class="li">aaa</div>
   </div>
</div>
```

```css
.box {
    width: 1200px;
    background-color: orange;
    margin: auto;
}
.ul {
    overflow: hidden;
    margin-right: -20px;
}
.li {
    width: 386.66px;
    height: 300px;
    margin-right: 20px;
    background-color: green;
    float: left;
}
```

*两栏等高布局*

如果容器没有设置高度，margin正直可以使高度变大，负值可以使高度变小。

利用margin可以改变元素占据空间特性

```html
<div class="box">
    <div class="child-green">
        <div>left</div>
        <div>left</div>
        <div>left</div>
    </div>
    <div class="child-orange">
        <div>right</div>
    </div>
</div>
```

```css
.box {
    resize: vertical;
    overflow: hidden;
}
.child-green, .child-orange {
    float: left;
    padding: 0 200px;
    margin-bottom: -600px;
    padding-bottom: 600px;
}
.child-green {
    background-color: green;
}
.child-orange {
    background-color: orange;
}
```

*两栏自适应布局*

利用元素占据空间随margin移动的特性

```html
<div class="box">
   <div>
       <p>中华人民共和国，中华人民共和国，中华人民共和国，中华人民共和国，中华人民共和国，中华人民共和国，中华人民共和国，中华人民共和国，中华人民共和国，中华人民共和国，中华人民共和国</p>
   </div>
   <img src="" alt="">
</div>
```

```css
* {
    margin: 0;
    padding: 0;
}
.box {
    background-color: #000;
    overflow: hidden;
    color: #fff;
}
.box div, .box img {
    float: left;
}
.box div {
    width: 100%;
}
.box div p {
    margin-right: 200px;
}
.box img {
    width: 200px;
    height: 200px;
}
img {
    margin-left: -200px;
}
```

*总结*

|               | 不设宽   | 定宽     | 不设高                                                | 定高         |
| ------------- | -------- | -------- | ----------------------------------------------------- | ------------ |
| 正常block元素 | 增加宽度 | 宽度不变 | top负值，本身上移；bottom负值，下面元素上移           | 占据空间变小 |
| 浮动元素      | 位置移动 | 位置移动 | top负值，本身上移；bottom负值，下面元素上移至浮动底部 | 同左         |

#### margin失效解析

- inline元素垂直方向无效（非替换元素）
- 重叠
- display: table-cell时margin无效（非替换元素）
- position: absolute元素非定位方位（指没有设置方向值的方向）的margin无效（因为脱离文档流，所以不会影响其它元素，只是看起来无效）
- 鞭长莫及时无效，浮动时，margin值是相对于容器的，而不是兄弟元素
- 内联元素不会跑到容器外部，大于容器的margin负值无效

#### margin-start & margin-end

- 当文档流变化时，margin-start会根据变化设置不同margin开始的位置
- 当文档流变化时，margin-end会根据变化设置不同margin结束的位置

#### 参考

[CSS深入理解之margin](https://www.imooc.com/learn/680)

