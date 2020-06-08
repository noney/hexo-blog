---
title: sass文档-数据类型-number
date: 2020-06-08 11:28:47
tags:
    - 日积月累
    - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`sass`中`number`类型包括两个部分，数字本身和单位（可以没有单位，也可以有复数单位）。

```scss
@debug 100; // 100
@debug 0.8; // 0.8
@debug 16px; // 16px
@debug 5px * 2px; // 10px*px (read "square pixels")
```

Sass数字支持与CSS数字相同的格式，包括科学表示法，该表示法在数字和它的10次方之间使用一个e。由于浏览器对科学表示法的支持历来参差不齐，Sass总是将其编译为完全扩展的数字。

```scss
@debug 5.2e3; // 5200
@debug 6e-2; // 0.06
```

<!-- more -->

#### 单位

- 两个数相乘，它们的单位也相乘 `4px * 6px; // 24px*px`
- 两个数相除，它们的单位`一个数的单位 / 第二个数的单位` `5px / 2s; // 2.5px/s`
- 一个数的分子分母可以有任意多个 `5px * 30deg / 2s / 24em; // 3.125px*deg/s*em`

***注意：*** 我们随时可以使用`@debug`规则来检查任何变量或表达式

`sass`会自动在兼容的单位之间进行转换，然后选择一个单位做为结果单位，如果出现不兼容的单位，它将会抛出错误

```scss
// CSS defines one inch as 96 pixels.
@debug 1in + 6px; // 102px or 1.0625in

@debug 1in + 1s;
//     ^^^^^^^^
// Error: Incompatible units s and in.
```

在现实世界的单位计算中，如果分子包含与分母兼容的单位(比如96px / 1in)，它们就会被抵消。这样就可以很容易地定义用于单位之间转换的比率。在下面的示例中，我们将所需的速度设置为1秒/ 50像素，然后将该速度乘以过渡覆盖的像素数，以获得所需的时间。

```scss
$transition-speed: 1s/50px;

@mixin move($left-start, $left-stop) {
    position: absolute;
    left: $left-start;
    transition: left ($left-stop - $left-start) * $transition-speed;

    &:hover {
    left: $left-stop;
    }
}

.slider {
    @include move(10px, 120px);
}
```

```css
.slider {
    position: absolute;
    left: 10px;
    transition: left 2.2s;
}
.slider:hover {
    left: 120px;
}
```

***注意：***如果计算结果出现错误，需要检查一下是否遗忘了单位。避免使用`#{$number}px`这样的插值，它创建的不是一个`数字`，而是一个字符串，不能与任何数字操作和函数一起工作，可以在定论`$number`时加上单位，或者`$number * 1px`让它有单位

```scss
$number: 10px;

.box {
    width: $number + 10;
}

.dev {
    height: #{$number} + 10;
}
```

```css
.box {
    width: 20px;
}

.dev {
    height: 10px10;
}
```

`sass`中`50% 不等于 0.5`，它是一个独立单位，可以通过计算对它们进行转换`50% / 100%`转换为小数，`0.5 * 100%`转换为百分比，还可以使用`math.percentage`将小数转换为相应的百分比

#### 精度

`sass`支持小数点后`10`位

```scss
@debug 0.012345678912345; // 0.0123456789
@debug 0.01234567891 == 0.01234567899; // true
@debug 1.00000000009; // 1
@debug 0.99999999991; // 1
```

