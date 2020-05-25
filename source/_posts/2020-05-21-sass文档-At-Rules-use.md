---
title: sass文档-At-Rules-@use
date: 2020-05-21 17:53:53
tags:
 - 日积月累
 - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`@use`规则从其他`Sass`样式表加载`mixin、函数和变量`，并将来自多个样式表的`CSS`组合在一起。`@use`加载的样式表称为`“模块”`。`Sass`还提供了内置的模块，这些模块都是有用的功。

`@use "<url>"`加载模块，无论加载多少次，在编译输出的`css`中只会包含一次。

***注意：*** `@use`规则必须位于除`@forward`之外的任何规则之前，包括样式规则。但是，您可以在使用`@use`规则之前`声明变量`。

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
@use 'foundation/code';
@use 'foundation/lists';
```

```css
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

#### 加载模块

默认情况下，`url`的最后部分作为模块的`命名空间`，可以通过它访问模块的`变量、mixin、函数`。通过`@use`加载的模块，`变量、mixin、函数`只在当前样式表可见。如果希望同时从多个文件加载成员，可以使用@forward规则将所有成员从一个共享文件中转发出去。

```scss
// src/_corners.scss
$radius: 3px;

@mixin rounded {
    border-radius: $radius;
}
```

```scss
// style.scss
@use "src/corners";

.button {
    @include corners.rounded;
    padding: 5px + corners.$radius;
}
```

```css
.button {
    border-radius: 3px;
    padding: 8px;
}
```

通过`@use "<url>" as <namespace>`方式改变默认的命令空间

```scss
// src/_corners.scss
$radius: 3px;

@mixin rounded {
    border-radius: $radius;
}
```

```scss
// style.scss
@use "src/corners" as c;

.button {
    @include c.rounded;
    padding: 5px + c.$radius;
}
```

```css
.button {
    border-radius: 3px;
    padding: 8px;
}
```

通过`@use "<url>" as *`来加载匿名空间，为了避免冲突，尽量少用这种方式

```scss
// src/_corners.scss
$radius: 3px;

@mixin rounded {
    border-radius: $radius;
}
```

```scss
// style.scss
@use "src/corners" as *;

.button {
    @include rounded;
    padding: 5px + $radius;
}
```

```css
.button {
    border-radius: 3px;
    padding: 8px;
}
```

通过`-或者_`来定义私有成员

```scss
// src/_corners.scss
$-radius: 3px;

@mixin rounded {
    border-radius: $-radius;
}
```

```scss
// style.scss
@use "src/corners";

.button {
    @include corners.rounded;

    // 这里是错误的! $-radius 只能在 `_corners.scss`里面可见。
    padding: 5px + corners.$-radius;
}
```

通过`!default`定义可配置的变量，然后通过`@use <url> with (<variable>: <value>, <variable>: <value>)`来覆盖可配置变量

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
// style.scss
@use 'library' with (
    $black: #222,
    $border-radius: 0.1rem
);
```

```css
code {
    border-radius: 0.1rem;
    box-shadow: 0 0.5rem 1rem rgba(34, 34, 34, 0.15);
}
```

与`mixin`配合，如果想在模块加载后更新配置，这并不是一个很好的办法，考虑编写一个`mixin`来设置变量，并编写另一个`mixin`来注入您的样式。

```scss
// _library.scss
$-black: #000;
$-border-radius: 0.25rem;
$-box-shadow: null;

/// If the user has configured `$-box-shadow`, returns their configured value.
/// Otherwise returns a value derived from `$-black`.
@function -box-shadow() {
    @return $-box-shadow or (0 0.5rem 1rem rgba($-black, 0.15));
}

@mixin configure($black: null, $border-radius: null, $box-shadow: null) {
    @if $black {
        $-black: $black !global;
    }
    @if $border-radius {
        $-border-radius: $border-radius !global;
    }
    @if $box-shadow {
        $-box-shadow: $box-shadow !global;
    }
}

@include styles {
    code {
        border-radius: $border-radius;
        box-shadow: $box-shadow;
    }
}
```

```scss
// style.scss
@use 'library';

@include library.configure(
    $black: #222,
    $border-radius: 0.1rem
);

@include library.styles;
```

#### 查找模块

在不写`扩展名`的情况下，它会默认查找`.scss、.sass、.css`文件。为了确保样式表在每个操作系统上都能工作，Sass根据URL而不是文件路径加载文件。这意味着您需要使用`/`，而不是`\`，即使在`Windows`上也是如此。

```scss
@use 'src/module/library'
```

`sass`首先加相对于当前文件加载，如果找不到的情况下，才会使用`加载路径`。如`@use susy`，会加载`node_modules/susy/sass/susy.scss`。当我们想使用相对路径时，不需要使用`./`，`sass`默认会使用相对路径。

当`scss`文件使用`_`开头时，会被认为是`partials`。`sass`只会当作模块加载，而不去编译它们。在我们导入`_`开头时的模块时，我们可以省略掉`_`。

```scss
// foundation/_code.scss
code {
    padding: .25em;
    line-height: 0;
}
```

```scss
@use 'code'
```

当我们使用`@use`加载文件夹本身时，它会自动加载文件夹下的 `index.scss 或 index.sass`文件。

```scss
// foundation/_code.scss
code {
    padding: .25em;
    line-height: 0;
}
```

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
// foundation/_index.scss
@use 'code';
@use 'lists';
```

```scss
// style.scss
@use 'foundation';
```

```css
CSS OUTPUT
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

#### 加载css文件

`@use`同样也可以加载普通的`css`文件

```css
// code.css
code {
    padding: .25em;
    line-height: 0;
}
```

```scss
// style.scss
@use 'code'
```

```css
code {
    padding: .25em;
    line-height: 0;
}
```

***注意：***作为模块加载的CSS文件不允许任何特殊的Sass特性，因此不能公开任何Sass变量、函数或混合。为了确保作者不会不小心在他们的CSS中写入Sass，所有不是有效CSS的Sass特性都会产生错误。否则，CSS将按原样呈现。它甚至可以扩展!

#### 参考

[@use](https://sass-lang.com/documentation/at-rules/use)