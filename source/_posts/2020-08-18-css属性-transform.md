---


title: css属性 transform
date: 2020-08-18 10:43:30
tags:
  - 日积月累
  - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`css3`中提供了`transform`属性对元素应用2D 或 3D 转换。它可以使元素旋转、缩放、移动或倾斜

#### 浏览器支持

- IE `>=10 transform、9 -ms-transform、<9 不支持`
- Chrome、Safari 添加`-webkit-`前缀
- Firefox `transform`
- Opera `transform`（只支持 2D 转换）

<!-- more -->

#### 属性值

- none 默认值，不进行转换
- matrix 矩阵，实现复杂的转换，[工具](https://www.html.cn/tool/css3Preview/Transform-Matrix.html)
- matrix3d 矩阵3D转换
- translate 位移

| 属性        | 作用          | 备注                                                |
| ----------- | ------------- | --------------------------------------------------- |
| translateX  | x轴位移       | 支持`px`和`百分比`值，`百分比`值为元素本身宽度      |
| translateY  | y轴位移       | 支持`px`和`百分比`值，`百分比`值为元素本身高度      |
| translateZ  | z轴位移       | 只支持`px`值，必须在父元素上设置perspective（视距） |
| translate   | x、y轴位移    | 省略第二个参数，y轴位移为0                          |
| Translate3d | x、y、z轴位移 |                                                     |

***注意：***正数时表示正向移动，负数为反向移动

```html
<div class="box">
  <div class="translate">我是一个好人啊</div>
</div>
```

```css
.box {
  perspective: 100px;
}
.translate {
  transform: translate3d(20px, 10px, 30px)
  -ms-transform: translate3d(20px, 10px, 30px)
  -webkit-transform: translate3d(20px, 10px, 30px)
}
```

- scale 缩放

| 属性        | 作用          | 备注                                  |
| ----------- | ------------- | ------------------------------------- |
| scaleX  | x轴缩放       | 只支持`数值` |
| scaleY  | y轴缩放       | 只支持`数值` |
| scaleZ  | z轴缩放       | 只支持`数值` |
| scale   | x、y轴缩放    | 省略第二个参数，y轴缩放为1（不缩放）       |
| scale3d | x、y、z轴缩放 | 只支持`数值` |

***注意：***数值`大于1放大`，`小于1缩小`，`等于1不缩放`，`负值是翻转`

```html
<div class="box">
  <div class="translate">我是一个好人啊</div>
</div>
```

```css
.translate {
  transform: scale3d(-0.5, 0.5, 1);
  -ms-transform: scale3d(-0.5, 0.5, 1);
  -webkit-transform: scale3d(-0.5, 0.5, 1);
}
```

- rotate 旋转

| 属性    | 作用          | 备注                                 |
| ------- | ------------- | ------------------------------------ |
| rotateX  | x轴旋转       | 旋转角度                     |
| rotateY  | y轴旋转       | 旋转角度                     |
| rotateZ  | z轴旋转       | 旋转角度                     |
| rotate   | z轴旋转 | 旋转角度 |
| rotate3d | x、y、z轴旋转 | 前三个参数取值为-1（反向旋转）、0（不旋转）、1（正向旋转），第四个参数为旋转角度 |

***注意：***旋转角度，单位为 `deg`，正数时表示`顺时针旋转`，为负数时表示`逆时针旋转`

```html
<div class="box">
  <div class="rotate">我是一个好人啊</div>
  <div class="rotate1">我是一个好人啊</div>
  <div class="rotate2">我是一个好人啊</div>
  <div class="rotate3">我是一个好人啊</div>
</div>
```

```css
.rotate, .rotate1, .rotate2, .rotate3 {
  width: 200px;
  height: 200px;
  background-color: pink;
  transform: rotate(20deg);
  margin: 20px;
}
.rotate1 {
  background-color: green;
  transform: rotateX(20deg);
}
.rotate2 {
  background-color: red;
  transform: rotateY(20deg);
}
.rotate3 {
  background-color: green;
  transform: rotateZ(1, 1, 1, 20deg);
}
```

- skew 倾斜

| 属性  | 作用       | 备注                           |
| ----- | ---------- | ------------------------------ |
| skewX | x轴倾斜    | 倾斜角度                       |
| skewY | y轴倾斜    | 倾斜角度                       |
| skew  | x、y轴倾斜 | 省略第二个参数，y轴角度倾斜为0 |

***注意：***倾斜角度，单位为 `deg`，正数时表示`顺时针倾斜`，为负数时表示`逆时针倾斜`

```html
<div class="box">
  <div class="skew">我是一个好人啊</div>
  <div class="skew1">我是一个好人啊</div>
  <div class="skew2">我是一个好人啊</div>
</div>
```

```css
.skew, .skew1, .skew2, .skew3 {
  width: 200px;
  height: 200px;
  background-color: pink;
  transform: skew(20deg);
  margin: 20px;
}
.skew1 {
  background-color: green;
  transform: skewX(20deg);
}
.skew2 {
  background-color: red;
  transform: skewY(20deg);
}
```

#### 视距

`perspective`表示观察者（也就是电脑前面的你）距离图像的距离，是绘制`3D`效果必不可少的属性，它有两种使用方式

- 直接加在`transform`属性中

```css
.rotate {
  transform: perspective(100px);
}
```

- 加在转换元素的父元素上

```css
.box {
  perspective: 100px;
}
.rotate {
  transform: rotate3d(-1, 0, 1, 30deg);
}
```

#### 参考

[特效属性「Transform」+ 矩阵 matrix](https://www.jianshu.com/p/f943e2014c39)
[CSS3 transform 属性](https://www.w3school.com.cn/cssref/pr_transform.asp)