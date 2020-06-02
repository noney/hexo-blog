---
title: sass文档-At-Rules-error-warn-debug
date: 2020-06-02 15:37:03
tags:
        - 日积月累
        - css



---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

当`@mixin、@function`在接收参数时

- 如果不是我们预期的格式和类型时，可以用`@error`来中断并报错
- 如果传递的值并不是理想的值时，可以用@warn来做一个警告。

我们可以使用`@debug`来做调试，可以使用它来打印`变量或者表达式`的值，同时还会输出`文件名和行号`。

#### error

`@error <expression>` 打印输出错误

<!-- more -->

```scss
@mixin reflexive-position($property, $value) {
    @if $property != left and $property != right {
        @error "Property #{$property} must be either left or right.";
    }

    $left-value: if($property == right, initial, $value);
    $right-value: if($property == right, $value, initial);

    left: $left-value;
    right: $right-value;
    [dir=rtl] & {
        left: $right-value;
        right: $left-value;
    }
}

.sidebar {
    @include reflexive-position(top, 12px);
    //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    // Error: Property top must be either left or right.
}
```

命令行输出

```shell
Error: "Property top must be either left or right."
     ╷
18 │   @include reflexive-position(top, 12px);
     │   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
     ╵
    style.scss 18:3  root stylesheet
```

#### warn

`@warn <expression>` 打印输出错误，`css`照样输出

```scss
$known-prefixes: webkit, moz, ms, o;

@mixin prefix($property, $value, $prefixes) {
    @each $prefix in $prefixes {
        @if not index($known-prefixes, $prefix) {
            @warn "Unknown prefix #{$prefix}.";
        }

        -#{$prefix}-#{$property}: $value;
    }
    #{$property}: $value;
}

.tilt {
    // Oops, we typo'd "webkit" as "wekbit"!
    @include prefix(transform, rotate(15deg), wekbit ms);
}
```

```css
.tilt {
    -wekbit-transform: rotate(15deg);
    -ms-transform: rotate(15deg);
    transform: rotate(15deg);
}
```

命令行输出

```shell
Warning: Unknown prefix wekbit.
        style.scss 6:7   prefix()
        style.scss 16:3  root stylesheet
```

#### debug

`@debug <expression>` 输出`debug`信息

```scss
@mixin inset-divider-offset($offset, $padding) {
    $divider-offset: (2 * $padding) + $offset;
    @debug "divider offset: #{$divider-offset}";

    margin-left: $divider-offset;
    width: calc(100% - #{$divider-offset});
}

.box {
        @include inset-divider-offset(20px, 30px);
}
```

```css
.box {
    margin-left: 80px;
    width: calc(100% - 80px);
}
```

命令行输出

```shell
style.scss:3 Debug: divider offset: 80px
```

***注意：***您可以将任何值传递给`@debug`，而不仅仅是字符串!它打印该值的表示形式与`meta.inspect()`函数相同



#### 参考

[@error](https://sass-lang.com/documentation/at-rules/error)
[@warn](https://sass-lang.com/documentation/at-rules/warn)
[@debug](https://sass-lang.com/documentation/at-rules/debug)