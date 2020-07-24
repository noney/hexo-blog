---
title: sass文档-数据类型-function
date: 2020-07-24 18:08:10
tags:
  - 日积月累
  - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

通过`meta.get-function()`函数，可以将函数当作一个值传入另一个函数，此时在另一个函数内可以使用`meta.call()`函数来调用它

```scss
@use "sass:list";
@use "sass:meta";
@use "sass:string";

/// Return a copy of $list with all elements for which $condition returns `true`
/// removed.
@function remove-where($list, $condition) {
  $new-list: ();
  $separator: list.separator($list);
  @each $element in $list {
    @if not meta.call($condition, $element) {
      $new-list: list.append($new-list, $element, $separator: $separator);
    }
  }
  @return $new-list;
}

$fonts: Tahoma, Geneva, "Helvetica Neue", Helvetica, Arial, sans-serif;

content {
  @function contains-helvetica($string) {
    @return string.index($string, "Helvetica");
  }
  font-family: remove-where($fonts, meta.get-function("contains-helvetica"));
}
```

<!-- more -->

```css
content {
  font-family: Tahoma, Geneva, Arial, sans-serif;
}
```

#### 参考

[Functions](https://sass-lang.com/documentation/values/functions)