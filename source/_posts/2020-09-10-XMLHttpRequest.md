---
title: XMLHttpRequest
date: 2020-09-10 11:10:37
tags:
  - 日积月累
  - ajax

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`XMLHttpRequest`对象用于与服务端数据进行异步（或同步）交互

#### 构造函数

```javascript
const xhr = new XMLHttpRequest() // 返回xhr对象
```
#### 属性

- readyState（只读）请求状态

  | 值   | 状态             | 描述                                           | 备注                     |
  | ---- | ---------------- | ---------------------------------------------- | ------------------------ |
  | 0    | UNSET            | `xhr`被创建，但未调用`open`方法                | 请求未初始化             |
  | 1    | OPENED           | `open`方法被调用                               | 服务器连接已建立         |
  | 2    | HEADERS_RECEIVED | `send` 方法被调用，并且头部和状态已经可获得。  | 请求已接收               |
  | 3    | LOADING          | 下载中； `responseText` 属性已经包含部分数据。 | 请求处理中               |
  | 4    | DONE             | 下载操作已完成。                               | 请求已完成，且响应已就绪 |

<!-- more -->

- status（只读）响应状态码 [HTTP status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Response_codes)

  ***注意：*** 请求完成前（UNSENT、OPENED）和`xhr`对象出错，`status`值为`0`

- statusText（只读）响应状态文本

  ***注意：*** 请求完成前（UNSENT、OPENED）`statusText`值为空；服务端未指定自动赋值`OK`

- response（只读）返回响应正文

- responseText（只读） 返回响应正文

  ***注意：*** 请求完成前（UNSENT、OPENED）`responseText`值为空；请求失败时值为`null`

- responseType 告诉服务端我们期望的数据类型，并告诉`xhr.response`根据什么格式解析数据（解析失败，`xhr.response`值为`null`）

| 属性值       | 返回值           |
| ------------ | ---------------- |
| ""（默认值） | 等于responseText |
| arraybuffer  | arraybuffer      |
| document     | 等于responseXML  |
| json         | javascript对象   |
| text         | 等于responseText |

​ ***注意：***当我们设置了`responseType`属性，我们获取响应正文应该是`response`，而不是`responseText`

- responseURL（只读）序列化后的URL，不包括错点
- responseXML（只读）返回HTML或者XML文档

#### 方法

- abort 终止请求（readyState 和 status 均被置为`0`）
- getAllResponseHeaders 返回所有响应头，没有收到响应返回`null`
- getResponseHeader 返回指定的响应头中的项，不存在返回`null`
- open 初始化请求

| 参数    | 描述                     |
| ------- | ------------------------ |
| method  | GET、POST、PUT、DELETE等 |
| url     | 请求地址                 |
| sync    | 是否异步，默认为`true`   |
| user    | 用户名，默认为`null`     |
| assword | 密码，默认为`null`       |

- overrideMimeType 覆盖由服务器返回的类型 
- send 发送请求
- setRequestHeader 设置`HTTP`请求头

#### 事件

- onreadystatechange `xhr.readyState`改变的时候触发（`abort`方法不会触发它）
- abort 执行`xhr.abort`方法时触发
- error 请求错误时触发
- load 请求完成时触发
- loadend 请求结束时触发（无论是`load`、`error`、`abort`都会触发）
- loadstart 接收到响时数据时触发
- progress 接收数据过程中触发
- timeout 超时触发

#### 参考

[XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)