---
title: sass文档-At-Rules-extend
date: 2020-05-29 15:25:44
tags:
                - 日积月累
                - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`@extend`扩展样式，解决一个类中拥有其它类相同的样式和属于自己的样式，这时我们就可以扩展相同的样式。

```html
<div class="error error--serious">
    Oh no! You've been hacked!
</div>
```

```scss
.error {
    border: 1px #f00;
    background-color: #fdd;

    &--serious {
        @extend .error;
        border-width: 3px;
    }
}
```

<!-- more -->

```css
.error, .error--serious {
    border: 1px #f00;
    background-color: #fdd;
}
.error--serious {
    border-width: 3px;
}
```

***注意：***扩展只针对父选择器，不会扩展子选择器的样式

```scss
.error {
    color: red;
    &--circle {
        border: 1px solid red;
    }
}

.warn {
    @extend .error;
    height: 200px;
}
```

```css
.error, .warn {
    color: red;
}
.error--circle {
    border: 1px solid red;
}

.warn {
    height: 200px;
}
```

### 如何工作

`@extend`与`@mixin`不同，`@mixin`是将样式复制到当前样式规则中。`@extend`是对`选择器`做扩展。

- 它不会生成像`#main #sub`的选择器，因为它们不会匹配任何元素
- 它确保复杂的选择器是交错的，这样无论HTML元素是按什么顺序嵌套的，它们都可以工作
- 它尽可能地减少冗余选择器，同时仍然确保特异性大于或等于extender
- 它知道一个选择器何时匹配另一个选择器所做的一切，并可以将它们组合在一起

```scss
.content nav.sidebar {
    @extend .info;
}

// 这个不会被扩展，因为p和nav不兼容
p.info {
    background-color: #dee9fc;
}

// 不确定.guide会在.content里面还是外面，所以两种情况都生成
.guide .info {
    border: 1px solid rgba(#000, 0.8);
    border-radius: 2px;
}

// 匹配main.content肯定也会匹配.content，所以不会产生不必要的交换选择器
main.content .info {
    font-size: 0.8em;
}
```

```css
p.info {
    background-color: #dee9fc;
}

.guide .info, .guide .content nav.sidebar, .content .guide nav.sidebar {
    border: 1px solid rgba(0, 0, 0, 0.8);
    border-radius: 2px;
}

main.content .info, main.content nav.sidebar {
    font-size: 0.8em;
}
```

#### 占位符选择器

`%`代表点位符选择器，可以用来进行简单的样式扩展，它必须使用`@extend`来调用，否则不会在`css`中有任何输出

```scss
.alert:hover, %strong-alert {
    font-weight: bold;
}

%strong-alert:hover {
    color: red;
}

div {
    @extend %strong-alert
}
```

```css
.alert:hover, div {
    font-weight: bold;
}

div:hover {
    color: red;
}
```

与模块成员一样，可以使用`-_`来标记私有占位符，它只能在定义它的样式表中使用

```scss
// _alert.scss
$color: red;

%-alert {
    background-color: red;
}
```

```scss
@use "alert";

.box {
    color: alert.$color;
    @extend %-alert; /*报错*/
}
```

#### 扩展作用域

使用`@use或者@forward`加载的模块，扩展的作用域只会影响上游的模块。使用`@import`加载的模块，扩展的作用域是全局的，不仅会影响上游还会影响下游，也就是说它会影响整个样式表。

#### 强制和可选扩展

默认在使用`@extend`时，如果没有可匹配的选择器，`sass`会产生一个错误，这有助于防止输入错误或重命名选择器。如果我们不需要这个错误时，只需在未尾添加`!optional`。

#### 何时使用`@extend`和`@mixin`

- mixin 需要参数配置时，样式的非语义集合，使用`mixin`可以避免级联问题，并简化配置
- extend 纯样式块扩展时，表示语义类之间的关系时，如`.error、.error-serious`

#### 限制

只有简单的选择器才能被扩展，如`.info、a`，如果`.message.info`可以被扩展，那么`@extend`的定义表明，与扩展器匹配的元素将被设计成与`.message.info`匹配的样式。这和匹配`.message`和`.info`是一样的，所以可以用这个代替`@extend .message, .info。

类似地，如果`.main .info`可以被扩展，它会做(几乎)与扩展`.info`相同的事情。细微的差别不值得让人觉得它在做一些本质上不同的事情，所以这也是不允许的。

```scss
.alert {
    @extend .message.info;
    //      ^^^^^^^^^^^^^
    // Error: Write @extend .message, .info instead.

    @extend .main .info;
    //      ^^^^^^^^^^^
    // Error: write @extend .info instead.
}
```

#### HTML启发式

当`@extend`交错复杂的选择器时，它不会生成所有可能的祖先选择器组合。它可能生成的许多选择器实际上不太可能匹配实际的`HTML`，并且生成它们会使样式表太大而没有实际价值。相反，它使用了一种启发式方法:它假设每个选择器的祖先将是自包含的，而不与任何其他选择器的祖先交错

```scss
header .warning li {
    font-weight: bold;
}

aside .notice dd {
    // Sass doesn't generate CSS to match the <dd> in
    //
    // <header>
    //   <aside>
    //     <div class="warning">
    //       <div class="notice">
    //         <dd>...</dd>
    //       </div>
    //     </div>
    //   </aside>
    // </header>
    //
    // because matching all elements like that would require us to generate nine
    // new selectors instead of just two.
    @extend li;
}
```

#### 媒体查询中扩展

虽然`@extend`允许在`@media`和其他`CSS at`规则中使用，但不允许扩展出现在其`at`规则之外的选择器。这是因为扩展选择器只适用于给定的媒体上下文中，而且无法确保在不复制整个样式规则的情况下保留生成的选择器中的限制。

```scss
@media screen and (max-width: 600px) {
    .error--serious {
        @extend .error;
        //      ^^^^^^
        // Error: ".error" was extended in @media, but used outside it.
    }
}

.error {
    border: 1px #f00;
    background-color: #fdd;
}
```

#### 参考

[@extend](https://sass-lang.com/documentation/at-rules/extend)