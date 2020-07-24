---
title: sass文档-数据类型-lists
date: 2020-07-24 14:26:53
tags:
  - 日积月累
  - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`sass`中使用`逗号或者空格`来分列表项（必须一致，不能混用），列表项可以使用`()、[]`包裹，也可以不用包裹，列表项为空时，使用`()、[]`表示

```scss
$sizes0: 0, 0;
$sizes1: 1px 1px;

$sizes2: ();
$sizes3: (3px 3px);
$sizes4: (4px, 4px);

$sizes5: [];
$sizes6: [6px, 6px];
$sizes7: [7px 7px];
```

<!-- more -->

#### 列表应用

- 使用`list.nth`函数获取列表指定索引的值（下标从`1`开始，`-1`代表最后一个列表项）

```scss
@use "sass:list";

$sizes0: 0, 0;

.list {
  font-size: list.nth($sizes0, 1);
}
```

```css
.list {
  font-size: 0;
}
```

- 使用`@each`遍历

```scss
@use "sass:list";

$sizes0: 0, 1px;

@each $size in $sizes0 {
  .list-#{$size} {
    font-size: $size;
  }
}
```

```css
.list-0 {
  font-size: 0;
}

.list-1px {
  font-size: 1px;
}
```

- 使用`list.append`函数追加列表项，返回一个`新的列表`，原列表不变

```scss
@use "sass:list";

$sizes0: 0, 1px;
$sizes1: list.append($sizes0, 2px);

@each $size in $sizes1 {
  .list-#{$size} {
    font-size: $size;
  }
}
```

```css
.list-0 {
  font-size: 0;
}

.list-1px {
  font-size: 1px;
}

.list-2px {
  font-size: 2px;
}
```

- 使用`list.index`查找列表项，返回索引值，如果没找到返回`null`

```scss
@use "sass:list";

$sizes0: 0;

.list {
  @if (list.index($sizes0, 1)) {
    font-size: 10px;
  }
  @else {
    font-size: 20px;
  }
}
```

```css
.list {
  font-size: 20px;
}
```

#### 不变性

`sass`列表不可变，函数操作返回一个新列表，不过我们可以将`新列表`赋值给`原列表变量`来更新它

```scss
@use "sass:list";
@use "sass:map";

$prefixes-by-browser: ("firefox": moz, "safari": webkit, "ie": ms);

@function prefixes-for-browsers($browsers) {
  $prefixes: ();
  @each $browser in $browsers {
  $prefixes: list.append($prefixes, map.get($prefixes-by-browser, $browser));
  }
  @return $prefixes;
}

@debug prefixes-for-browsers("firefox" "ie"); // moz ms
```

#### 参数列表

当我们定义一个`函数`或者`mixin`接收任意参数时，我们可以得到一个`参数列表`，如果用户传递的是`关键字参数`，我们还可以使用`meta.keywords()`函数，来做为映射访问

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

#### 参考

[Lists](https://sass-lang.com/documentation/values/lists)