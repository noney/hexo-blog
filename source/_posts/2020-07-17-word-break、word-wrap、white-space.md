---
title: word-break、word-wrap、white-space
date: 2020-07-17 10:48:40
tags:
  - 日积月累
  - css

---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

总结word-break、word-wrap、white-space用法。

```html
<div class="box">
  Hi&nbsp;&nbsp;,
  This   is a incomprehensibilities long word.
  </br>
  你好&nbsp;&nbsp;，
  这   是一个不可思议的长单词
</div>
```

```css
.box {
  width: 100px;
  border: 1px solid red;
}
```

<!-- more -->

我们根据上面`html`的显示可以得出如下总结：

- `&nbsp;和</br>`都正常发挥作用
- 连续的`空格`会被缩减成`一个`
- `换行符`无效
- `空格`会触发换行
- `长英文`会超出边界
- `长中文`会自动换行

***注意：***`默认自动换行（非手动回车换行）`，当`宽度`不足时，由`空格`触发，`连续中文`会`换行`，`长英文`不会`换行`

## white-space

`white-space`用来控制`空白字符`的显示，同时也能控制`自动换行`

| 属性值   | 换行符（手动回车） | 空格       | 默认自动换行 |
| -------- | ------------------ | ---------- | ------------ |
| normal   | 无效               | 缩减成一个 | 有效         |
| nowrap   | 无效               | 缩减成一个 | 无效         |
| pre      | 有效               | 有效       | 无效         |
| pre-wrap | 有效               | 有效       | 有效         |
| pre-line | 有效               | 无效       | 有效         |

#### word-break

`word-break`用来控制单词如何拆分

| 属性值    | 表现   | 备注                             |
| --------- | ------ | -------------------------------- |
| normal    | 默认值 | 默认自动换行（中文拆，英文不拆） |
| keep-all  | 不拆分 | 都不拆                           |
| break-all | 拆分   | 都拆（慎用，英文会被拆开）       |

与`white-space`同时使用的情况

| 属性      | normal | nowrap | pre  | pre-wrap | pre-line |
| --------- | ------ | ------ | ---- | -------- | -------- |
| keep-all  | 有效   | 有效   | 有效 | 有效     | 有效     |
| break-all | 有效   | 无效   | 无效 | 有效     | 有效     |

***注意：***`有效/无效`是针对`word-break`属性来说的

#### word-wrap

`wrord-wrap`也是用来控制单词拆分，`css3`中已经被重名字为`overflow-wrap`，现在它被作为`别名`

| 属性值     | 表现   | 备注                                                         |
| ---------- | ------ | ------------------------------------------------------------ |
| ormal      | 默认值 | 默认自动换行（中文拆，英文不拆）                             |
| break-word | 拆分   | 都拆（当一个单词一行放不下时才会拆分，`break-all`的不同是只要到边界就拆） |

#### 应用

`单行`超出部分用点显示

```css
.box {
  width: 100px;
  border: 1px solid red;
  white-space: nowrap;  // 不换行
  text-overflow: ellipsis; // 点点点
  overflow: hidden; // 超出部分隐藏
}
```

`多行`超出部分用点显示

```css
.box {
  width: 100px;
  border: 1px solid red;
  overflow: hidden; /*隐藏超出部分*/
  display: -webkit-box; /* 必须结合的属性，将对象作为弹性伸缩盒子模型显示 */
  -webkit-line-clamp: 2; /* 用来限制在一个块元素显示的文本的行数，它需要组合其他的WebKit属性 */
  line-clamp: 2;
  -webkit-box-orient: vertical; /* 必须结合的属性，设置或检索伸缩盒对象的子元素的排列方式 */
}
```



#### 参考

[css超出部分省略号](https://blog.csdn.net/liguanjie8/article/details/83068731)
[彻底搞懂word-break、word-wrap、white-space](https://www.cnblogs.com/dfyg-xiaoxiao/p/9640422.html)