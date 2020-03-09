---


title: 理解css border
date: 2020-03-02 14:17:25
tags:
     - 日积月累
     - CSS
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://noney.coding.me/)*

__作者：[Noney Li]__

#### 写在前面

**border**表示边框。它包含三个属性`border-width、border-style、border-color`分别表示宽度、样式和颜色，我们通常是用它的简写形式`border:1px solid red`。

#### border-width 不支持百分比

**border-width**属性是不支持百分比的。我们平时应用的时候不会让它随设备或者元素的大小而改变，它只是表达一个固定的宽度，所以不支持百分比也是根据它的作用和语义而决定的。

它支持一些关键字，`thin（1px）、medium（3px）、thick（5px）`等（IE7除外），它的默认宽度是**3px**，因为像`border-style:dobule`这类样式是需要**3px**才能有效果。

<!-- more -->

#### border-style 类型

dashed 虚线，有兼容性问题，区别如下：

|                | IE   | Chrome/FireFox |
| -------------- | ---- | -------------- |
| 实色区域宽高比 | 2:1  | 3:1            |
| 间隔区域宽高比 | 2:1  | 1:1            |

dotted 点线，有兼容性问题，区别如下：

|                | IE   | Chrome/FireFox |
| -------------- | ---- | -------------- |
| 实色区域宽高比 | 圆形 | 正方形         |

我们可以利用这个特性，在IE7、8上面实现圆角

```html
<div class="parent">
    <div class="child">dotted</div>
</div>
```

```css
.parent {
    width: 150px;
    height: 150px;
    overflow: hidden;
}
.child {
    width: 100%;
    height: 100%;
    border: 149px dotted red;
}
```

double 双实线，兼容性非常好，根据它的等分原则，我们可以用它来实现三道杠的图形效果。

```html
<div class="double"></div>
```

```css
.double {
    width: 120px;
    height: 20px;
    border-top: 60px double;
    border-bottom: 20px solid;
}
```

#### border-color与color

border-color的默认颜色就是color，当我们没有指定边框颜色的时候，它会使用color属性的值作为颜色值。

#### border与background定位

定位不会计算border区域。

#### border与三角等图形的构建

利用solid实现三角。

```html
<div class="triangle"></div>
```

```css
        .triangle {
            width: 0;
            height: 0;
            border: 20px solid red;
            border-color: red transparent transparent transparent;
        }
```

模拟圆角

```html
<span class="circle-btn">button</span>
```

```css
        .circle-btn {
            padding: 15px 50px;
            color: #fff;
            background-color: blue;
            display: inline-block;
            margin: 20px;
            position: relative;
        }
        .circle-btn::before {
            content: "";
            display: inline-block;
            border: 3px solid;
            border-color: transparent transparent blue transparent;
            width: 96%;
            position: absolute;
            top: -6px;
            left: 0;
        }
        .circle-btn::after {
            content: "";
            display: inline-block;
            border: 3px solid;
            border-color: blue transparent transparent transparent;
            width: 96%;
            position: absolute;
            bottom: -6px;
            left: 0;
        }
```

#### border与透明边框

增加点击区域

```html
<input type="checkbox" id="check">
<label class="checkbox" for="check"></label>
```

```css
.checkbox {
    width: 16px;
    height: 16px;
    border: 2px solid transparent;
    box-shadow: inset 0 1px, inset 1px 0, inset -1px 0, inset 0 -1px;
    background-color: #fff;
    background-clip: content-box;
    color: #d0d0d5;
    display: inline-block;
}
```

#### border在布局中的应用

实现两栏等高布局

```html
<div class="box">
    <nav class="left">
        <h3>导航1</h3>
    </nav>
    <section>
        <div class="module1">模块1</div>
    </section>
    <div class="clear"></div>
</div>
```

```css
.box {
    border-left: 300px solid #222;
}
.left {
    width: 300px;
    margin-left: -300px;
    float: left;
    color: #fff;
}
.clear {
    clear: both;
}
```

缺点：不支持百分比。

#### 参考

[CSS深入理解之border](https://www.imooc.com/learn/755)