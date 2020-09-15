---
title: 前端二进制操作之Object URLs
date: 2020-09-08 17:24:05
tags:
	- 日积月累
	- 二进制

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`Object URLs`是一种伪协议，它指向一个二进制源，所以也会被称为`Blog URL`。它是由`URL.createObjectURL`方法创建

#### 语法

```javascript
let objUrl = URL.createObjectURL(new Blob(['hello, world'])
```

#### 参数

参数可以是一个`File`对象、`Blob`对象或者`MediaSource`对象

```html
<input type="file" onchange="change(event)">
<img src="" alt="" id="VidwImg">
```

<!-- more -->

```javascript
function change(event) {
	let file = event.target.files[0],
		fr = new FileReader(),
		vi = document.querySelector('#VidwImg');
	vi.src = URL.createObjectURL(file);
}
```

#### 返回值

`blob:<origin>/<uuid>`  其中`uuid`是唯一的

#### 特性

每次调用`createObjectURL`方法时，都会创建一个新的`URL`对象，而它们都会存在内存中，只有当当前窗口关闭的时候，浏览器才会释放它们，内存回收，所以我们需要在适当的时机调用`URL.revokeObjectURL`方法来释放内存。

#### 与Data URL的区别

`Object URLs`是一个指针，指向内存中的`Blob`对象

- 优点 很短，不会增加`html`大小
- 缺点 不回收会一直占用内存，只会活在当前文档中，文档关闭即失效

`Data URLs`是一段很长的字符串

- 优点 可以减少http请求数，可以用于网络传输
- 缺点 由于其是通过`base64`编码的（会比原文件大1/3），所以会增加`html`大小，影响加载速度

#### 参考

[URL.createObjectURL()](https://developer.mozilla.org/zh-CN/docs/Web/API/URL/createObjectURL)
[玩转前端二进制](https://juejin.im/post/6846687590783909902)