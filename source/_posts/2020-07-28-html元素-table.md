---
title: html元素-table
date: 2020-07-28 17:31:48
tags:
  - 日积月累
  - html


---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

使用`table`元素可以创建一个表格。它由多个`tr、th、td`组成，它还包括`caption、col、colgroup、thead、tfoot` 以及` tbody` 元素

#### table元素

| 属性        | 值                                                        | 描述                                                         | 备注                                 |
| ----------- | --------------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------ |
| align       | left、center、right                                       | 默认`table`自己占一行，`align`属性可以达到`float`效果，使其它元素围绕显示 | ~~不赞成使用。请使用样式取而代之。~~ |
| bgcolor     | rgb(x,x,x)、#xxxxxx、colorname                            | 表格背景颜色                                                 | ~~不赞成使用。请使用样式取而代之。~~ |
| border      | pixels                                                    | 规定表格边框的宽度。                                         |                                      |
| cellpadding | pixels 、%                                                | 单元格内边距                                                 |                                      |
| cellspacing | pixels 、%                                                | 单元格外边距                                                 |                                      |
| frame       | void、above、below、hsides、lhs、rhs、vsides、box、border | 规定外侧边框的哪个部分是可见的                               | ~~不赞成使用。兼容差~~               |
| rules       | none、groups、rows、cols、all                             | 规定内侧边框的哪个部分是可见的                               | ~~不赞成使用。兼容差~~               |
| summary     | text                                                      | 表格摘要，针对屏幕阅读器                                     | 不显示在浏览器里                     |
| width       | pixels 、%                                                | 表格宽度                                                     | 默认自身宽度，最好使用`css`定宽      |

<!-- more -->

***align 例子***

```html
<table border="1" align="right">
  <tr>
  <th>Month</th>
  <th>Savings</th>
  </tr>
  <tr>
  <td>January</td>
  <td>$100</td>
  </tr>
</table>
<p>align 属性可以使文本围绕在表格周围。</p>
<p>在 HTML 4.01 中，不赞成使用 body 元素的 align 属性；在 XHTML 1.0 Strict DTD 中，不支持 body 元素的 align 属性</p>
```

***总结：*** 大多数属性都不建议使用或者建议使用`css`样式设置，我们需要关注的是`cellpadding、cellspacing`这两个属性

`table`特性：

- 包裹性
- 不设置宽度，不会超过`父容器`的宽度

#### caption元素

`caption` 元素定义表格标题

| 属性  | 值                  | 描述             | 备注                                 |
| ----- | ------------------- | ---------------- | ------------------------------------ |
| align | left、center、right | 规定标题对齐方式 | ~~不赞成使用。请使用样式取而代之。~~ |

#### col&colgroup元素

兼容性不好，谨慎使用

#### thead&tbody&tfoot元素

`thead、tbody、tfoot`三个元素仅得到所有主流浏览器的`部分`支持。如果要使用它们，三个元素必须同时使用

#### tr元素

`tr`元素定义表格中的行。它包含`td、th`

| 属性     | 值                                                          | 描述                             | 备注                                 |
| :------- | :---------------------------------------------------------- | :------------------------------- | ------------------------------------ |
| align    | right、left、center、justify（IE 居中显示）、char（兼容差） | 定义表格行的内容对齐方式。       | 水平对齐                             |
| bgcolor  | rgb(x,x,x)、#xxxxxx、colorname                              | 规定表格行的背景颜色。           | ~~不赞成使用。请使用样式取而代之。~~ |
| char     | character                                                   | 规定根据哪个字符来进行文本对齐。 | ~~不赞成使用。兼容差~~               |
| char off | number                                                      | 规定第一个对齐字符的偏移量。     | ~~不赞成使用。兼容差~~               |
| valign   | top、middle、bottom、baseline                               | 规定表格行中内容的垂直对齐方式。 | 垂直对齐                             |

***总结：***大多数属性兼容性都很差，我们需要关注的是`align、valign`这两个属性

`tr`特性

- 包裹性
- `margin`无效
- `width`无效，它的宽度由`td`或`table`决定

#### td&th元素

`td`元素定义表格中的标准单元格。内部文本`正常大小、左对齐`显示

`th`元素定义表格中的表头单元格。内部文本`居中、加粗`显示

除了以上区别，其它方面几乎没有任何区别，它们有着`同样的属性`

| 属性    | 值                                                         | 描述                                                 | 备注                                                         |
| ------- | ---------------------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------ |
| bbr     | text                                                       | 单元格中内容的缩写，针对屏幕阅读器                   | 不显示在浏览器里                                             |
| align   | left、center、right、justify（IE居中显示）、char（兼容差） | 单元格内容的水平对齐方式                             | 水平对齐                                                     |
| axis    | category_name                                              | 单元格分类                                           | ~~~~不赞成使用。兼容差~~                                     |
| bgcolor | rgb(x,x,x)、#xxxxxx、colorname                             | 单元格背景颜色                                       | ~~不赞成使用。请使用样式取而代之。~~                         |
| char    | character                                                  | 规定根据哪个字符来进行内容的对齐                     | ~~~~不赞成使用。兼容差~~                                     |
| charoff | number                                                     | 规定对齐字符的偏移量                                 | ~~~~不赞成使用。兼容差~~                                     |
| colspan | number                                                     | 可横跨的`列数`                                       | 没有浏览器支持 colspan="0"（指示浏览器横跨到列组的最后一列） |
| rowspan | number                                                     | 可横跨的`行数`                                       | 没有浏览器支持 colspan="0"（指示浏览器横跨到列组的最后一行） |
| headers | header_cells'_id                                           | 相关的表头，针对屏幕阅读器                           | 不显示在浏览器里                                             |
| height  | pixels、%                                                  | 高度                                                 | ~~不赞成使用。请使用样式取而代之。~~                         |
| owrap   | nowrap                                                     | 内容是否折行                                         | ~~不赞成使用。请使用样式取而代之。~~                         |
| scope   | col、colgroup、row、rowgroup                               | 定义将表头数据与单元数据相关联的方法，针对屏幕阅读器 | 不显示在浏览器里                                             |
| valign  | top、middle、bottom、baseline                              | 内容的垂直排列方式                                   | 垂直对齐                                                     |
| width   | pixels、%                                                  | 宽度                                                 | ~~不赞成使用。请使用样式取而代之。~~                         |

***总结：***大多数属性兼容性都很差，我们需要关注的是`align、valign、colspan、rowspan`这四个属性

`td`特性

- 包裹性
- `margin`无效
- 高度相等
- 宽度等分（fixed）

#### table&tr&td总结

| table宽度 | 单元格宽度 | table-layout | 单元格表现                                    | table表现                                                | 备注                           |
| --------- | ---------- | ------------ | --------------------------------------------- | -------------------------------------------------------- | ------------------------------ |
| 不设置    | 不设置     | auto/fixed   | 内容宽度                                      | 所有单元格宽度之和（内容宽度）                           | 不会超出`table父容器`宽度      |
| 设置      | 不设置     | auto         | 内容宽度                                      | 设置的`table`宽度                                        | 内容多时，没有空白，单元格变高 |
| 设置      | 不设置     | fixed        | 宽度均分                                      | 设置的`table`宽度                                        |                                |
| 设置      | 设置       | auto         | 内容宽度+空白                                 | 设置的`table`宽度                                        | 宽度均分                       |
| 设置      | 设置       | fixed        | table大，宽度均分；单元格大，单元格设置的宽度 | table大，设置的`table`宽度；单元格大，所有单元格宽度之和 |                                |
| 不设置    | 设置       | auto/fixed   | 设置的单元格宽度                              | 所有单元格宽度之和                                       | 单元格相等                     |

***结论：***

- `fixed`时，单元格宽度相等（除了都不设置宽度），所有`单元格`宽度之和与`table`宽度比较，取大者。`table`宽度大时，`单元格`均分；反之`table`的宽度为所有`单元格`宽度之和（此规则均以`css`设置的宽度为准）
- `auto`时，单元格宽度等于`内容宽度+剩余空间`（除了最后一条），如果内容过多，`剩余空间`将不存在，并且单元格增加高度（此规则均以`内容`的宽度为准）
- 都不设置宽度时，`table`内容不会超出父容器

#### vertical-align

`vertical-align`属性可以用来设置元素的垂直对齐方式。它也能应用于`td、th`元素

#### 合并边框

当我们给`table、td`添加边框时，就会出现双层边框情况，此时我们需要使用`border-collapse`属性合并边框

- separate 默认值，边框被分开
- collapse 边框会合并为一个单一的边框
- inherit 从父元素继成`任何的版本的 Internet Explorer （包括 IE8）都不支持属性值 "inherit"`

```html
<table>
  <tr>
    <td>11111111111111</td>
  </tr>
</table>
```

```css
table {
  border: 1px solid red;
  border-collapse: collapse;
}
table tr {

}
table td {
  border: 1px solid red;
}
```

#### 表格布局算法

表格布局算法使用`css table-layout`属性

- auto 默认值，列宽度由单元格`内容`设定（`max-width、min-width`有效）
- fixed 列宽由表格宽度和列宽度设定（所有`td`宽度之和与`table`宽度两者取最大值，`max-width、min-width`无效）
- inherit 从父元素继成`任何的版本的 Internet Explorer （包括 IE8）都不支持属性值 "inherit"`

#### 实例-滚动条

```html
<div class="tbox">
  <table>
    <tr>
      <th>one</th>
      <th>two</th>
      <th>three</th>
      <th>four</th>
      <th>five</th>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
    <tr>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
      <td>111111111111</td>
    </tr>
  </table>
</div>
```

```css
.tbox {
  width: 1000px;
  margin: 0 auto;
  overflow-x: scroll;
}
.tbox table {
  border: 1px solid red;
  border-collapse: collapse;
  table-layout: fixed;
  width: 100%; // 不设置此宽度，table宽度由td决定，当所有宽度之和大于table时，td设置宽度无效，宽度平分table父级宽度
}
.tbox table tr {

}
.tbox table tr td, .tbox table tr th {
  border: 1px solid red;
  text-align: center;
  padding: 5px 10px;
  width: 200px;
}
```

#### 参考

[border-collapse](https://www.w3school.com.cn/cssref/pr_tab_border-collapse.asp)
[table-layout](https://www.w3school.com.cn/cssref/pr_tab_table-layout.asp)
[HTML table 标签](https://www.w3school.com.cn/tags/tag_table.asp)
[HTML tr 标签](https://www.w3school.com.cn/tags/tag_tr.asp)
[HTML td 标签](https://www.w3school.com.cn/tags/tag_td.asp)
[HTML th 标签](https://www.w3school.com.cn/tags/tag_th.asp)
[【CSS】Table样式](https://www.cnblogs.com/peterYong/p/10761757.html)
[display: table-cell知识点总结](https://www.jianshu.com/p/52b0c2a7ea29)