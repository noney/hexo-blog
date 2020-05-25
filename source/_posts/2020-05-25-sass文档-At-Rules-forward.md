---
title: sass文档-At-Rules-forward
date: 2020-05-25 15:13:21
tags:
  - 日积月累
  - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`@forward`可以用来将多个`partials`集合起来，使用`@use`加载它们的`mixin、函数、变量`。

```scss
// forms/_index.scss
@forward 'input';
@forward 'textarea';
@forward 'select';
@forward 'buttons';
```

```scss
// styles.scss
@use 'forms'; // 导入`forms` 命名空间下的所有被转发的成员
```

<!-- more -->

#### 添加前缀

通过`@forward "<url>" as <prefix>-*`方式来添加前缀，它会为模块的每个`mixin、函数、变量`添加前缀。

```scss
// src/_list.scss
@mixin reset {
    margin: 0;
    padding: 0;
    list-style: none;
}
```

```scss
// bootstrap.scss
@forward "src/list" as list-*;
```

```scss
// styles.scss
@use "bootstrap";

li {
    @include bootstrap.list-reset;
}
```

```css
li {
    margin: 0;
    padding: 0;
    list-style: none;
}
```

#### 控制显示

有时，我们可能希望只显示一部分`mixin、函数、变量`，我们可以使用`@forward "<url>" hide <members...>`来控制哪些成员被隐藏，使用`@forward "<url>" show <members...>`控制哪些成员被显示。

```scss
// src/_list.scss
$horizontal-list-gap: 2em;

@mixin list-reset {
    margin: 0;
    padding: 0;
    list-style: none;
}

@mixin list-horizontal {
    @include reset;

    li {
  display: inline-block;
  margin: {
      left: -2px;
      right: $horizontal-list-gap;
  }
    }
}
```

```scss
// bootstrap.scss
@forward "src/list" hide list-reset, $horizontal-list-gap;
```

#### 配置模块

`@forword`也可以对模块进行配置。它和`@use`的区别是，它可以在配置中使用`!default`，允许下游覆盖它。

```scss
// _library.scss
$black: #000 !default;
$border-radius: 0.25rem !default;
$box-shadow: 0 0.5rem 1rem rgba($black, 0.15) !default;

code {
    border-radius: $border-radius;
    box-shadow: $box-shadow;
}
```

```scss
// _opinionated.scss
@forward 'library' with (
    $black: #222 !default,
    $border-radius: 0.1rem !default
);
```

```scss
// style.scss
@use 'opinionated' with ($black: #333);
```

#### 参考

[Sass Module 介绍](https://www.jianshu.com/p/ab700c9ba92a)
[@forward](https://sass-lang.com/documentation/at-rules/forward)