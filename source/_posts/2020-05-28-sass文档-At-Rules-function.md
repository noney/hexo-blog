---
title: sass文档-At-Rules-function
date: 2020-05-28 17:48:34
tags:
        - 日积月累
        - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`@function`可以用来进行样式的计算，可以在样式表的中重复调用。同变量名一样，`-_`是不区分的，也就是`reset-list与reset_list`表示同一个`函数`，这是历史遗留问题，在早期版本`sass`只允许`函数`中出现`_`。

```scss
@function pow($base, $exponent) {
    $result: 1;
    @for $_ from 1 through $exponent {
        $result: $result * $base;
    }
    @return $result;
}

.sidebar {
    float: left;
    margin-left: pow(4, 3) * 1px;
}
```

<!-- more -->

```css
.sidebar {
    float: left;
    margin-left: 64px;
}
```

***注意：***虽然函数在技术上可以设置全局变量，但是强烈不建议这样做。使用mixin来处理，使用函数来计算值。

#### 参数

- 可选参数 同`mxin`

```scss
@function invert($color, $amount: 100%) {
    $inverse: change-color($color, $hue: hue($color) + 180);
    @return mix($inverse, $color, $amount);
}

$primary-color: #036;
.header {
    background-color: invert($primary-color, 80%);
}
```

```css
.header {
    background-color: #523314;
}
```

- 关键字参数 同`mixin`

```scss
$primary-color: #036;
.banner {
    background-color: $primary-color;
    color: scale-color($primary-color, $lightness: +40%);
}
```

```css
.banner {
    background-color: #036;
    color: #0a85ff;
}
```

- 任意参数 同`mixin`

```scss
@function sum($numbers...) {
    $sum: 0;
    @each $number in $numbers {
        $sum: $sum + $number;
    }
    @return $sum;
}

.micro {
    width: sum(50px, 30px, 100px);
}
```

```css
.micro {
    width: 180px;
}
```

- 接受任意关键字参数 同`mixin`

- 传递任意关键字参数 同`mixin`

```scss
$widths: 50px, 30px, 100px;
.micro {
    width: min($widths...);
}
```

```css
.micro {
    width: 30px;
}
```

#### 返回值

`@return`返回计算结果，它只能出现在函数体内，函数必须以它结束。当函数遇到它时，立刻返回计算结果，忽略后面的计算。

```scss
@use "sass:string";

@function str-insert($string, $insert, $index) {
    // Avoid making new strings if we don't need to.
    @if string.length($string) == 0 {
        @return $insert;
    }

    $before: string.slice($string, 0, $index);
    $after: string.slice($string, $index);
    @return $before + $insert + $after;
}
```

#### 其它函数

除了自定义函数外，`sass`提示了很多`内置函数`，同时它也支持`原生css函数`。

除了自定义函数和内置函数外的其它函数，都会被编译为`css函数`，参数也会被编译为`css`，按原样包含在函数调用中。这样处理的好处就是在增加新的`css函数`时，不需要升级`sass`

```scss
@debug var(--main-bg-color); // var(--main-bg-color)

$primary: #f2ece4;
$accent: #e1d7d2;
@debug radial-gradient($primary, $accent); // radial-gradient(#f2ece4, #e1d7d2)
```

#### 参考

[@function](https://sass-lang.com/documentation/at-rules/function)