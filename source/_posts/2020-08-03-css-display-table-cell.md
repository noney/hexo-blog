---
title: css display table-cell
date: 2020-08-03 14:49:31
tags:
  - 日积月累
  - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`css2.1`表格模型中，为了使其能正常工作，如果`table/inline-table、table-row、table-cell`没有同时出现，浏览器会默认将其补全，在元素的周围生成匿名元素。

```html
<div class="cell">我是一个单元格</div>
<!-- 等价于 -->
<div class="table">
	<div class="row">
		<div class="cell">我是一个单元格</div>
	</div>
</div>
<!-- table、row是浏览器为了补全表格模型默认创建的，它们都是`匿名元素` -->
```

```css
.table {
	display: table;
}
.row {
	display: table-row;
}
.cell {
	display: table-cell;
}
```

<!-- more -->

反之，则有

```html
<div class="table">
	<p>我是一个单元格</p>
</div>
<!-- 等价于 -->
<div class="table">
	<div class="row">
		<div class="cell">我是一个单元格</div>
	</div>
</div>
<!-- cell、row是浏览器为了被全表格模型默认创建的，它们都是`匿名元素` -->
```

```css
.table {
	display: table;
}
.row {
	display: table-row;
}
.cell {
	display: table-cell;
}
```

#### 应用

- 多行元素垂直居中

```html
<div class="cell">
	<p>我是一行文字</p>
	<p>我是一行文字</p>
	<p>我是一行文字</p>
</div>
```

```css
.cell {
	display: table-cell;
	width: 200px;
	height: 200px;
	vertical-align: middle;
	border: 1px solid red;
}
```

***解析：***利用单元格元素可以使用`vertical-align`来设置内容垂直方式特性

- 自适应两栏布局（左侧固定，右侧自适应）

```html
<div class="box">
	<div class="left">
		<p>我是左边</p>
	</div>
	<div class="right">
		<p>我是右边我是右边我是右边我是右边我是右边我是右边我是右边我是右边我是右边我是右边</p>
	</div>
</div>
```

```css
.box {
	border: 1px solid green;
}
.left {
	float: left;
	width: 200px;
}
.right {
	display: table-cell;
	width: 9999px;
	background-color: blue;
	color: #fff;
}
```

***解析：***利用`table`（此时是匿名元素）不设置宽度，它的宽度等于单元格`内容`宽度并且单元格宽度不会超出`table`的父元素宽度。

- 等高布局

```html
<div class="box">
	<div class="left">
		<p>我是左边</p>
	</div>
	<div class="right">
		<p>我是右边</p>
		<p>我是右边</p>
	</div>
</div>
```

```css
.box {
	border: 1px solid green;
	display: table-row;
}
.left, .right {
	display: table-cell;
}
.left {
	background-color: purple;
}
.right {
	background-color: blue;
	color: #fff;
}
```

***解析：***利用`table-row`下所有`table-cell`等高特性

- 等宽布局

```html
<table class="table" cellspacing="0" cellpadding="0">
	<tr>
		<td>我是一个Td</td>
		<td>我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td我是一个Td</td>
		<td>我是一个Td</td>
	</tr>
</table>
```

```css
.table {
	width: 500px;
	table-layout: fixed;
}
td {
	background-color: green;
}
```

***解析：***利用`fixed`时，`table-cell`宽度均分特性

#### 参考

[display: table-cell知识点总结](https://www.jianshu.com/p/52b0c2a7ea29)
[html元素-table](http://0kv30q.coding-pages.com/2020/07/28/html元素-table/)