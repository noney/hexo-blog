---
title: http Content-Type
date: 2020-09-10 17:25:52
tags:
  - 日积月累
  - ajax

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

`Content-Type`字面意思就是内容类型，也叫做[MIME](http://www.baidu.com)类型。在`HTTP`中，存在于`request和response`中

#### request

在`HTTP request`中，我们可以通过下面几种方式设置它

- `xhr`对象的`setRequestHeader`方法，默认值`text/plain;charset=UTF-8`

```javascript
xhr.setRequestHeader("Content-type","application/json"); // application/json 浏览器不进行任何处理
```

- `form`表单中设置`enctype`属性

| 值                                          | 描述                                    |
| ------------------------------------------- | --------------------------------------- |
| application/x-www-form-urlencoded（默认值） | 在发送前编码所有字符                    |
| multipart/form-data                         | 不对字符编码                            |
| text/plain                                  | 空格转换为 "+" 加号，但不对特殊字符编码 |

<!-- more -->

- `FormData`构造函数，默认值为`multipart/form-data;`

***总结：***`request`中设置`Content-type`值，浏览器会根据类型对数据进行处理（或者不处理），服务端会根据类型使用相应方法获取数据

#### rsponse

在`HTTP response`中，可以在发送数据之前设置类型。如果没有显示设置，`MIME`嗅探会自动设置相应类型。当浏览器获取到`Content-type`后，会根据`xhr.responseType`属性进行处理，返回预期类型数据，如果解析错误，`xhr.resonse`返回`null`。如果没有设置`xhr.responseType`属性，我们可能通过`xhr.getResponseHeader('Content-Type')`方法获取返回类型，根据返回类型对`xhr.responseText`属性值进行相应处理。

***注意：***如果服务端没有返回正确`Content-Type`，我们还可以使用`xhr.overrideMimeType`方法来覆盖它，然后再进行相应处理

#### 参考

[Content-Type 详解](https://www.jianshu.com/p/de5845b4c095)

[HTML <form> 标签的 enctype 属性](https://www.w3school.com.cn/tags/att_form_enctype.asp)

[Content-Type](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type)