---
title: sass文档-数据类型-Booleans
date: 2020-07-24 17:20:46
tags:
  - 日积月累
  - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`sass`使用`true、false`来表示布尔值。等式、关系操作符、一些内置函数的的返回值，都是布尔值。

```scss
@use "sass:map";

@debug 1 == 2; // false
@debug 2 < 3; // true

@debug map.has-key((), width); //false
```

<!-- more -->

布尔值运算

```scss
@debug true and true; // true
@debug true and false; // false

@debug true or false; // true
@debug false or false; // false

@debug not true; // false
@debug not false; // true
```

#### 使用Boolean

```scss
@mixin res($isBorder: false) {
  @if $isBorder {
    border: 1px solid red;
  }
}

.boo {
  width: 10px;
  @include res();
}

.boo-true {
  height: 10px;
  @include res(true);
}
```

```css
.boo {
  width: 10px;
}

.boo-true {
  height: 10px;
  border: 1px solid red;
}
```

`if()`函数，第一个参数为真，返回第二个函数，参数为假，返回第三个函数，相当于其它语言的三元运算符

```scss
@debug if(true, 10px, 20px); // 10px
@debug if(false, 10px, 20px); // 20px
```

#### 注意

`sass`中，除了`false、null`运算结果为`false`，其它都为`true`

#### 参考

[Booleans](https://sass-lang.com/documentation/values/booleans)