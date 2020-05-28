---
title: sass文档-At-Rules-mixin-and-include
date: 2020-05-28 14:52:56
tags:
    - 日积月累
    - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`@mixin`用来定义`重用`的样式，使用`@include`调用。可以放在单个样式规则中、可以包含自己的样式规则、可以嵌套在其它样式中、可以包含在样式表的顶层、可以用它来修改变量。

```scss
@mixin reset-list {
    margin: 0;
    padding: 0;
    list-style: none;
}

@mixin horizontal-list {
    @include reset-list;

    li {
    display: inline-block;
    margin: {
        left: -2px;
        right: 2em;
    }
    }
}

nav ul {
    @include horizontal-list;
}
```

<!-- more -->

```css
nav ul {
    margin: 0;
    padding: 0;
    list-style: none;
}
nav ul li {
    display: inline-block;
    margin-left: -2px;
    margin-right: 2em;
}
```

***注意：***同变量名一样，`-_`是不区分的，也就是`reset-list与reset_list`表示同一个`mixin`，这是历史遗留问题，在早期版本`sass`只允许`mixin`中出现`_`。

#### 参数

`@mixin`允许传递参数

```scss
@mixin rtl($property, $ltr-value, $rtl-value) {
    #{$property}: $ltr-value;

    [dir=rtl] & {
    #{$property}: $rtl-value;
    }
}

.sidebar {
    @include rtl(float, left, right);
}
```

```css
.sidebar {
    float: left;
}
[dir=rtl] .sidebar {
    float: right;
}
```

#### 可选参数

使用`变量名:默认值`让参数成为可选参数，如果不传值，变量就会使用默认值。

```scss
@mixin replace-text($image, $x: 50%, $y: 50%) {
    text-indent: -99999em;
    overflow: hidden;
    text-align: left;

    background: {
    image: $image;
    repeat: no-repeat;
    position: $x $y;
    }
}

.mail-icon {
    @include replace-text(url("/images/mail.svg"), 0);
}
```

```css
.mail-icon {
    text-indent: -99999em;
    overflow: hidden;
    text-align: left;
    background-image: url("/images/mail.svg");
    background-repeat: no-repeat;
    background-position: 0 50%;
}
```

#### 关键字参数

除了通过`位置`传递参数外，还可以通过关键字来传递参数。

```scss
@mixin kags($size, $width: 0) {
    height: $size;
    @if $width != 0 {
        width: $width;
    }
}

.box {
    @include kags($width: 30px, $size: 20px);
}
```

```css
.box {
    height: 20px;
    width: 30px;
}
```

#### 任意参数

通过`...`接收任意参数，但必须是放在参数的最后。

```scss
@mixin order($height, $selectors...) {
    @for $i from 0 to length($selectors) {
    #{nth($selectors, $i + 1)} {
        position: absolute;
        height: $height;
        margin-top: $i * $height;
    }
    }
}

@include order(150px, "input.name", "input.address", "input.zip");
```

```css
input.name {
    position: absolute;
    height: 150px;
    margin-top: 0px;
}

input.address {
    position: absolute;
    height: 150px;
    margin-top: 150px;
}

input.zip {
    position: absolute;
    height: 150px;
    margin-top: 300px;
}
```

#### 任意关键字参数

任意参数也可以是关键字参数，通过`meta.keywork`方法接收，返回`参数名:参数值`，参数名不包括`$`。

```scss
@use "sass:meta";

@mixin syntax-colors($args...) {
    @debug meta.keywords($args);
    // (string: #080, comment: #800, variable: $60b)

    @each $name, $color in meta.keywords($args) {
    pre span.stx-#{$name} {
        color: $color;
    }
    }
}

@include syntax-colors(
    $string: #080,
    $comment: #800,
    $variable: #60b,
)
```

```css
pre span.stx-string {
    color: #080;
}

pre span.stx-comment {
    color: #800;
}

pre span.stx-variable {
    color: #60b;
}
```

### 传递任意参数

不止是可以接受任意参数，也可以传递任意参数。

```scss
$form-selectors: "input.name", "input.address", "input.zip" !default;

@mixin order($size, $selectors...) {
    @for $i from 0 to length($selectors) {
    #{nth($selectors, $i + 1)} {
        position: absolute;
        height: $size;
        margin-top: $i * $size;
    }
    }
}

.gox {
    @include order(150px, $form-selectors...);
}
```

```css
.gox input.name {
    position: absolute;
    height: 150px;
    margin-top: 0px;
}
.gox input.address {
    position: absolute;
    height: 150px;
    margin-top: 150px;
}
.gox input.zip {
    position: absolute;
    height: 150px;
    margin-top: 300px;
}
```

#### 内容块

`@mixin`能过`@content`接受内容块。使用`{}`来传递内容块，传入的内容块会替换掉`@content`。

```scss
@mixin hover {
    &:not([disabled]):hover {
    @content;
    }
}

.button {
    border: 1px solid black;
    @include hover {
    border-width: 2px;
    }
}
```

```css
.button {
    border: 1px solid black;
}
.button:not([disabled]):hover {
    border-width: 2px;
}
```

通过`@content(<arguments...>)`给内容块传递参数，通过`@include <name> using (<arguments...>)`接收内容块传递来的参数。

```scss
@mixin media($types...) {
    @each $type in $types {
    @media #{$type} {
        @content($type);
    }
    }
}

@include media(screen, print) using ($type) {
    h1 {
    font-size: 40px;
    @if $type == print {
        font-family: Calluna;
    }
    }
}
```

```css
@media screen {
    h1 {
    font-size: 40px;
    }
}
@media print {
    h1 {
    font-size: 40px;
    font-family: Calluna;
    }
}
```

#### 语法缩写

`@use`可以缩写成`=`，`@include`可以缩写成`+`，随便短小，但不方便理解，尽量不要使用它。

```sass
SassCSS
SASS SYNTAX
=reset-list
    margin: 0
    padding: 0
    list-style: none

=horizontal-list
    +reset-list

    li
    display: inline-block
    margin:
        left: -2px
        right: 2em

nav ul
    +horizontal-list
```

```css
nav ul {
    margin: 0;
    padding: 0;
    list-style: none;
}
nav ul li {
    display: inline-block;
    margin-left: -2px;
    margin-right: 2em;
}
```

#### 参考

[@mixin and @include](https://sass-lang.com/documentation/at-rules/mixin)

