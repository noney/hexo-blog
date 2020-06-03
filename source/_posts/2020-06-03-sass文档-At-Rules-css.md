---
title: sass文档-At-Rules-css
date: 2020-06-03 15:25:54
tags:
    - 日积月累
    - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`sass`支持所有`CSS at-rule`规则。为了兼容未来的原生规则和向前兼容，`sass`提供了几乎覆盖所有的`CSS at-rule`规则，`CSS at-rule`写法`@<name> <value>, @<name> { ... }, or @<name> <value> { ... }`，名称必须是一个`标识符`，值可以是任何东西。名称和值都可以是`插值表达式`

```scss
@namespace svg url(http://www.w3.org/2000/svg);

@font-face {
    font-family: "Open Sans";
    src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2");
}

@counter-style thumbs {
    system: cyclic;
    symbols: "\1F44D";
}
```

<!-- more -->

如果`CSS at-rule`嵌套在样式规则中，那么这两个规则会自动交换位置，以便`at-rule`位于`CSS`输出的顶层，样式规则也在其中。这使得添加条件样式很容易，而不必重写样式规则的选择器。

```scss
.print-only {
    display: none;

    @media print { display: block; }
}
```

```css
.print-only {
    display: none;
}
@media print {
    .print-only {
    display: block;
    }
}
```

#### @media

除了允许`插值`之外，它还允许在特性查询中直接使用`SassScript`表达式

```scss
$layout-breakpoint-small: 960px;

@media (min-width: $layout-breakpoint-small) {
    .hide-extra-small {
    display: none;
    }
}
```

```css
@media (min-width: 960px) {
    .hide-extra-small {
    display: none;
    }
}
```

在可能的情况下，Sass还将合并彼此嵌套的媒体查询，以便更容易地支持还不支持嵌套@media规则的浏览器。

```scss
@media (hover: hover) {
    .button:hover {
    border: 2px solid black;

    @media (color) {
        border-color: #036;
    }
    }
}
```

```css
@media (hover: hover) {
    .button:hover {
    border: 2px solid black;
    }
}
@media (hover: hover) and (color) {
    .button:hover {
    border-color: #036;
    }
}
```

#### @supports

`@supports`规则还允许在声明查询中使用SassScript表达式

```scss
@mixin sticky-position {
    position: fixed;
    @supports (position: sticky) {
    position: sticky;
    }
}

.banner {
    @include sticky-position;
}
```

```css
.banner {
    position: fixed;
}
@supports (position: sticky) {
    .banner {
    position: sticky;
    }
}
```

#### @keyframes

它的子规则必须是有效的关键帧规则(<number>%， from, or to)，而不是普通的选择器

```scss
@keyframes slide-in {
    from {
    margin-left: 100%;
    width: 300%;
    }

    70% {
    margin-left: 90%;
    width: 150%;
    }

    to {
    margin-left: 0%;
    width: 100%;
    }
}
```

```css
@keyframes slide-in {
    from {
    margin-left: 100%;
    width: 300%;
    }
    70% {
    margin-left: 90%;
    width: 150%;
    }
    to {
    margin-left: 0%;
    width: 100%;
    }
}
```

#### 参考

[CSS At-Rules](https://sass-lang.com/documentation/at-rules/css#media)