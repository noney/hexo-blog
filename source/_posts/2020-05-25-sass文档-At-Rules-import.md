---
title: sass文档-At-Rules-import
date: 2020-05-25 16:36:58
tags:
    - 日积月累
    - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面   

`@import`能够导入`sass、css`样式表，加载`mixin、函数和变量`，并将来自多个样式表的`CSS`组合在一起。可以使用`,`分隔一次导入多个样式表。

***注意：***不建议继续使用`@import`，在将来会被移除。我们可以使用更友好的`@use、@forword`来导入和管理模块。迁移工具 [migration tool](https://sass-lang.com/documentation/cli/migrator)

```scss
// foundation/_code.scss
code {
    padding: .25em;
    line-height: 0;
}
```

<!-- more -->

```scss
// foundation/_lists.scss
ul, ol {
    text-align: left;

    & & {
        padding: {
            bottom: 0;
            left: 0;
        }
    }
}
```

```scss
// style.scss
@import 'foundation/code', 'foundation/lists';
```

```scss
code {
    padding: .25em;
    line-height: 0;
}

ul, ol {
    text-align: left;
}
ul ul, ol ol {
    padding-bottom: 0;
    padding-left: 0;
}
```

#### 加载文件

加载文件的规则与`@use`相同。

#### 嵌套

`@import`可以出现在样式规则、或者普通的`css at-root`中。导入的规则被限定在`块作用域中`，但定义的顶级mixin、函数和变量仍然是全局定义的。

```scss
// _theme.scss
pre, code {
    font-family: 'Source Code Pro', Helvetica, Arial;
    border-radius: 4px;
}
```

```scss
// style.scss
.theme-sample {
    @import "theme";
}
```

```css
.theme-sample pre, .theme-sample code {
    font-family: 'Source Code Pro', Helvetica, Arial;
    border-radius: 4px;
}
```

嵌套导入中的CSS像mixin一样计算，这意味着任何父选择器都将引用嵌套样式表的选择器。

```scss
// _theme.scss
ul li {
    $padding: 16px;
    padding-left: $padding;
    [dir=rtl] & {
        padding: {
            left: 0;
            right: $padding;
        }
    }
}
```

```scss
// style.scss
.theme-sample {
    @import "theme";
}
```

```css
.theme-sample ul li {
    padding-left: 16px;
}
[dir=rtl] .theme-sample ul li {
    padding-left: 0;
    padding-right: 16px;
}
```

#### 导入css

`@import`也可以导入普通的`.css`文件，唯一的规则就是不要带`.css`扩展名，这是它和`css import`的区别。

```scss
// code.css
code {
    padding: .25em;
    line-height: 0;
}
```

```scss
// style.scss
@import 'code';
```

```css
code {
    padding: .25em;
    line-height: 0;
}
```

***注意：***Sass导入的CSS文件不允许任何特殊的Sass特性。为了确保作者不会不小心在他们的CSS中写入Sass，所有不是有效CSS的Sass特性都会产生错误。否则，CSS将按原样呈现。它甚至可以扩展!

#### 原生css @import

`sass`支持原生`@import`，下面几种情况，编译时不会导入样式。

- `.css`结尾的`url` 

```scss
@import "theme.css";
```

- `url`以`http、https`开头的

```scss
@import "http://fonts.googleapis.com/css?family=Droid+Sans";
```

- 通过`url()`引入的

```scss
@import url(theme);
```

- 包含媒体查询的

```scss
@import "landscape" screen and (orientation: landscape);
```

`sass`导入不能使用插值，但是原生的导入可以，可以使用它做动态生成导入。

```scss
@mixin google-font($family) {
    @import url("http://fonts.googleapis.com/css?family=#{$family}");
}

@include google-font("Droid Sans");
```

#### 加载和模块

`@import`和`@use`可以做到无缝对接，也会相互兼容，但尽量使用`@use`。

#### 参考

[@import](https://sass-lang.com/documentation/at-rules/import#interpolation)