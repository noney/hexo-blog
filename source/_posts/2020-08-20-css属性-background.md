---
title: css属性-background
date: 2020-08-20 19:12:12
tags:
  - 日积月累
  - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`background`用于给元素设置一个或多个（IE8+）背景

#### 属性

- background-color 设置元素的背景颜色，颜色会填充元素的内容、内边距、边框区域（边框透明时，显示背景色）

```html
<div class="bgc">这是一个背景啊</div>
```

```css
.bgc {
  background-color: red;
  background: green;
  padding: 10px;
  border: 10px solid transparent;
}
```

<!-- more -->

| 颜色名称 | 十六进制值 | rgb/rgba                       | 透明（默认值） | inherit（IE8+） |
| -------- | ---------- | ------------------------------ | -------------- | --------------- |
| red      | #ff0000    | rgb(255,0,0) / rgb(255,0,0,.5) | transparent    | 继成的值        |

```html
<!-- .iner元素的属性之所有是绿色，并不是因为继成，而是因为它的默认背景为`transparent`，透明被填充了 -->
<div class="bgc"><div class="iner">这是一个背景啊</div></div>
```

```css
.bgc {
  background-color: green;
  padding: 10px;
  border: 10px solid transparent;
}
```

- background-position 设置背景图片的起始位置

```html
<div class="bgp"></div>
```

```css
.bgp {
  height: 700px;
  background: url('./test.jpg') no-repeat;
  background-color: blue;
  background-position: center center;
}
```

| 类型   | 值                                                           | 默认值  | 描述                         | 备注                               |
| ------ | ------------------------------------------------------------ | ------- | ---------------------------- | ---------------------------------- |
| 关键词 | `top left`，`top center`，`top right`，`center left`，`center center`，`center right`，`bottom left`，`bottom center`，`bottom right` | `%0 %0` | background-position: Y轴 X轴 | 如果只有一个值，另一个值为`center` |
| 百分比 | x% y%                                                        | 0% 0%   | background-position: X轴 Y轴 | 如果只有一个值，另一个值为`50%`    |
| 位置   | xpos ypos                                                    | 0% 0%   | background-position: X轴 Y轴 | 如果只有一个值，另一个值为`50%`    |

***总结：***百分比和位置值可以混合使用

***注意：***您需要把`background-attachment`属性设置为`fixed`，才能保证该属性在`Firefox`和`Opera`中正常工作。

- background-size 设置背景图片的尺寸

| IE   | Firefox | Chrome | Safari | Opera |
| ---- | ------- | ------ | ------ | ----- |
| 9+   | 4+      | 15+    | 7+     | 10.5+ |

```html
<div class="bgz"></div>
```

```css
.bgz {
  height: 700px;
  background: url('./test.jpg') no-repeat;
  background-color: blue;
  background-size: 50% 50%;
}
```

| 值      | 描述    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| 长度值  | 10% 10% | auto（图片本身宽高）                                         |
| 百分比  | x% y%   | 百分比值根据元素宽度计算                                     |
| cover   | cover   | 取元素`宽高`中的最`大`值，自适应图片，铺满元素，超过部分`被截取` |
| contain | contain | 取元素`宽高`中的最`小`值，自适应图片                         |
| auto    | 默认值  | 只有一个值时，另一个值为auto                                 |

- background-repeat 设置背景图片是否和如何重复

```html
<div class="bgr"></div>
```

```css
.bgr {
  height: 700px;
  background: url('./test.jpg') repeat-x;
  background-color: blue;
}
```

| 值        | 描述                         |
| --------- | ---------------------------- |
| repeat    | 水平及垂直方向重复（默认值） |
| repeat-x  | 水平方向重复                 |
| repeat-y  | 垂直方向重复                 |
| no-repeat | 水平及垂直方向不重复         |
| inherit   | 继成值                       |

- background-origin 设置背景图片`左上角`起点

| IE   | Firefox | Chrome | Safari | Opera |
| ---- | ------- | ------ | ------ | ----- |
| 9+   | 4+      | 15+    | 7+     | 10.5+ |

```html
<div class="bgr"></div>
```

```css
.bgr {
  height: 700px;
  background: url('./test.jpg') no-repeat;
  background-color: blue;
  padding: 100px;
  border: 20px solid transparent;
  background-origin: border-box;
}
```

| 值          | 描述                            |
| ----------- | ------------------------------- |
| padding-box | 起点为`padding`左边缘（默认值） |
| border-box  | 起点为`border`左边缘            |
| content-box | 起点为内容左边缘                |
| inherit     | 继成值                          |

- backgrond-clip 设置背景（不止是图片，包括颜色）`左上角`起点，多余部分将被裁剪

| IE   | Firefox | Chrome | Safari | Opera |
| ---- | ------- | ------ | ------ | ----- |
| 9+   | 4+      | 15+    | 7+     | 10.5+ |

```html
<div class="bgr"></div>
```

```css
.bgr {
  height: 700px;
  background: url('./test.jpg') no-repeat;
  background-color: blue;
  padding: 100px;
  border: 20px solid transparent;
  background-clip: content-box;
}
```

| 值          | 描述                           |
| ----------- | ------------------------------ |
| padding-box | 起点为`padding`左边缘          |
| border-box  | 起点为`border`左边缘（默认值） |
| content-box | 起点为内容左边缘               |
| inherit     | 继成值                         |

- background-attachment 设置背景图像是否跟随元素滚动

```html
<div class="bgr"></div>
<div class="part"></div>
```

```css
.bgr {
  height: 300px;
  background: url('./test.jpg') no-repeat;
  background-color: blue;
  background-origin: border-box;
  padding: 100px;
  border: 20px solid transparent;
  background-attachment: fixed;
}
.part {
  height: 100%;
  background-color: green;
}
```

| 值      | 描述               |
| ------- | ------------------ |
| scroll  | 跟随滚动（默认值） |
| fixed   | 背景固定           |
| inherit | 继成值             |

***注意：***设置`background-attchment: fixed`时，`background-origin`属性的值为`border-box`

- background-image 设置背景图片

| 值       | 描述                     |
| -------- | ------------------------ |
| None     | 不设置元素背景（默认值） |
| url(URL) | 指向图像路径             |
| inherit  | 继成值                   |

***总结：***

- `background-color`起点为`border-box`
- `background-image`起点为`padding-box`
- `backgroud-size`必须出现在`backgroud-position`之后，`backgroud-size`的两个属性值用`/`连接

#### 参考

[css属性分类介绍](https://www.cnblogs.com/xuanku/p/css_attr.html)

[CSS background 属性](https://www.w3school.com.cn/cssref/pr_background.asp)

