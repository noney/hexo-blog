---
title: sass文档-At-Rules-root
date: 2020-06-02 16:09:27
tags:
        - 日积月累
        - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`@root <selector> { ... }` 跳出嵌套，以文档的`根部`为起点。

```scss
@use "sass:selector";

@mixin unify-parent($child) {
    @at-root #{selector.unify(&, $child)} {
        @content;
    }
}

.wrapper .field {
    @include unify-parent("input") {
        /* ... */
    }
    @include unify-parent("select") {
        /* ... */
    }
}
```

<!-- more -->

```css
.wrapper input.field {
    /* ... */
}

.wrapper select.field {
    /* ... */
}
```

这里有必要使用`@root`，如果我们不使用它，生成的选择器是`.wrapper .field .wrapper input.field`，显示这不是我们想要的，我们不需要带上外层的选择器。

我们也可以将`@root <selector> { ... }`，写成`@root { <selector> {...}}`，同时还可以在里面，写多个选择器。

```scss
@use "sass:selector";

@mixin unify-parent($child) {
    @at-root {
        #{selector.unify(&, $child)} {
                @content;
        }
        .box {
                @content;
        }
    }
}

.wrapper .field {
    @include unify-parent("input") {
        /* ... */
    }
    @include unify-parent("select") {
        /* ... */
    }
}
```

```css
.wrapper input.field {
    /* ... */
}

.box {
    /* ... */
}

.wrapper select.field {
    /* ... */
}

.box {
    /* ... */
}
```

#### 排除样式规则

通过`@at-root (with: <rules...>) { ... }` or `@at-root (without: <rules...>) { ... }`来排除规则

```scss
@media print {
    .page {
        width: 8in;

        @at-root (without: media) { /*排除@media*/
            color: #111;
        }

        @at-root (with: rule) { /*保留样式规则*/
            font-size: 1.2em;
        }
    }
}
```

```css
@media print {
    .page {
        width: 8in;
    }
}
.page {
    color: #111;
}
.page {
    font-size: 1.2em;
}
```

除了`at-rules`的名字，还有两个特殊的值可以在查询中使用:

- `rule`指的是样式规则。例如，@at-root (with: rule)排除所有`at-rules`，但保留样式规则。
- `all`指的是所有`at-rules`和`rull`应该被排除。

#### 参考

[@at-root](https://sass-lang.com/documentation/at-rules/at-root)