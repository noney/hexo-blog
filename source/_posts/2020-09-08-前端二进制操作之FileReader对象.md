---
title: 前端二进制操作之FileReader对象
date: 2020-09-08 14:22:21
tags:
  - 日积月累
  - 二进制

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`FileReader`是一个构造函数，用来读取本地文件（或原始数据缓冲区）。使用`File`或`Blog`指定要读取的文件或内容

```html
  <input type="file" onchange="change(event)">
  <img src="" alt="" id="VidwImg">
```

- 读取`File`对象

```javascript
function change(event) {
  let file = event.target.files[0],
    fr = new FileReader(),
    vi = document.querySelector('#VidwImg');
  fr.onload = function() {
    console.log(`读取完成`, fr.result);
    vi.src = fr.result;
  }
  fr.readAsDataURL(event.target.files[0]);
}
```

<!-- more -->

- 读取`Blog`对象

```javascript
fetch('http://0kv30q.coding-pages.com/assets/blogImg/noney.png')
  .then((res) => res.blob())
  .then((blob) => {
    let ele = document.querySelector('#VidwImg'),
      fr = new FileReader();
    fr.onload = () => {
      ele.src = fr.result;
    }
    fr.readAsDataURL(blob);
  })
```

#### 属性

- error （只读）读取时发生错误，返回一个[`DOMException`](https://developer.mozilla.org/zh-CN/docs/Web/API/DOMException)对象，如何没有发生错误，返回`null`
- readyState（只读）读取状态

| 常量名  | 值   | 描述       |
| ------- | ---- | ---------- |
| EMPTY   | 0    | 还没有加载 |
| LOADING | 1    | 加载中     |
| DONE    | 2    | 加载完成   |

- result 文件内容，当`readyState`为`2`时才会有效，否则返回`undefined`

<!-- more -->

#### 事件

- onabort 中断时触发
- onload 读取完成时触发
- onerror 读取错误时触发
- onloadstart 开始读取时触发
- onloadend 读取结束时触发（成功、失败都会触发）
- onprogress 读取过程中触发   

#### 方法

- abort 中止读取 ，中止以后`readyState`值为`DONE`
- readAsArrayBuffer 读取结果作为`AarrayBuffer`返回
- readAsBinaryString 读取结果作为原始二进制返回
- readAsDataURL 读取结果作为`data:base64字符串`格式返回
- readAsText 读取结果作为字符串返回

### 参考

[FileReader](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader)