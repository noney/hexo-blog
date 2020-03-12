---
title: 理解css absolute
date: 2020-03-11 14:56:22
tags:
     - 日积月累
     - CSS
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面

absolute表示元素绝对定位。

#### absolute与float

- 包裹性
- 破坏性

#### 不受relative限制的absolute

没有relative，不使用top/right/left/right属性或使用auto作为值

- 脱离文档流 位置不变，显示在Z轴上方，不占用文档空间
- 去浮动，使浮动失效，两者不有共存
- 位置跟随，定位之前和定位之后的位置不变（IE7下定位之后元素变成display: inline-block，在定位外层添加一个空div）

<!-- more -->

*实例*

- 图片图标覆盖

```html
<div class="box">
    <span class="rec">推荐</span>
    <img src="img.jpg" alt=""><!--
    --><i class="vip"><em>vip</em></i>
</div>
```

```css
.rec {
    position: absolute;
    padding: 5px 10px;
    background-color: orange;
}
.box {
    width: 310px;
    height: 444px;
}
.box img {
    width: 100%;
}
.vip {
    position: absolute;
    color: #fff;
    border: 30px solid red;
    width: 0;
    height: 0;
    border-color: transparent red transparent transparent;
    transform: rotate(135deg);
    margin-left: -30px;
    margin-top: -30px;
}
.vip em {
    transform: rotate(-96deg);
    font-size: 20px;
    position: absolute;
    margin: -11px 0px 0px 3px;
}
```

- 如何定位下拉框

- 对齐居中或边缘

```html
<div class="box">
    <div class="f-box">
        &nbsp;
        <div class="f">
            <a href="http://www.imooc.com/activity/diaocha" class="goto_top_diaocha"></a>
            <a href="http://www.imooc.com/mobile/app" class="goto_top_app"></a>
            <a href="http://www.imooc.com/user/feedback" class="goto_top_feed"></a>
        </div>
    </div>
</div>
```

```css
.box {
    width: 800px;
    min-height: 1300px;
    margin: 0 auto;
    background-color: orange;
}
.f-box {
    text-align: right;
    overflow: hidden;
    height: 0;
}
.f {
    display: inline;
    position: fixed;
    margin-left: 25px;
    bottom: 10px;
}
.goto_top_diaocha, .goto_top_app, .goto_top_feed { 
    display: block; width: 48px; height: 48px; margin-top: 10px; background: url(http://img.mukewang.com/5453076e0001869c01920098.png) no-repeat; 
}
.goto_top_diaocha { 
    background-position: -48px 0; 
}
.goto_top_diaocha:hover { 
    background-position: -48px -50px; }
.goto_top_app { background-position: -96px 0; 
}
.goto_top_app:hover { 
    background-position: -96px -50px; 
}
.goto_top_feed { 
    background-position: -144px 0; 
}
.goto_top_feed:hover { 
    background-position: -144px -50px; 
}
```

#### 对齐或溢出技巧

```html
<div class="constr">
    <div class="regist-head">注册</div>
    <div class="regist-body">
        <div class="regist-main">
            <div class="regist-group">
                <label class="regist-label"><span class="regist-star">*</span>登录邮箱</label>
                <div class="regist-cell">
                    <input type="email" class="regist-input"><span class="regist-remark regist-warn">
                        <i class="icon-warn"></i>邮箱格式不准确（演示）
                    </span>
                </div>
            </div>
            <div class="regist-group">
                <label class="regist-label"><span class="regist-star">*</span>登录密码</label>
                <div class="regist-cell">
                    <input type="password" class="regist-input"><span class="regist-remark">
                        请输入6-16位密码，区分大小写，不能使用空格
                    </span>
                </div>
            </div>
            <div class="regist-group">
                <label class="regist-label"><span class="regist-star">*</span>用户昵称</label>
                <div class="regist-cell">
                    <input type="password" class="regist-input">
                </div>
            </div>
            <div class="regist-group">
                <label class="regist-label">手机号码</label>
                <div class="regist-cell">
                    <input type="tel" class="regist-input">
                </div>
            </div>
            <div class="regist-group">
                <label class="regist-label"><span class="regist-star">*</span>验 证 码</label>
                <div class="regist-cell">
                    <input class="regist-input regist-code-input"><img src="http://img.mukewang.com/545308540001678401500040.jpg">
                </div>
            </div>
            <div class="regist-group">
                <label class="regist-label">&nbsp;</label>
                <div class="regist-cell">
                    <input type="checkbox" checked><label>我已阅读并同意<a href="##">慕课协议</a>。</label>
                    <p>
                        <a href="javascript:" class="regist-btn">立即注册</a>
                    </p>
                </div>
            </div>
        </div>
    </div>
</div>
```

```css
a {
    color: #50B6E5;
}

.constr {
    width: 1200px;
    margin-left: auto;
    margin-right: auto;
}

.regist-head {
    height: 60px;
    line-height: 60px;
    padding-left: 30px;
    background-color: #be3948;
    color: #fff;
    font-size: 18px;
}

.regist-body {
    min-height: 400px;
    padding: 100px 0;
    background-color: #fff;
}

.regist-main {
    width: 600px;
    margin-left: auto;
    margin-right: auto;
}

.regist-group {
    margin-top: 20px;
    overflow: hidden;
}

.regist-label {
    width: 70px;
    padding-top: 10px;
    float: left;
}

.regist-cell {
    display: table-cell;
    *display: inline-block;
}

.regist-input {
    height: 18px;
    line-height: 18px;
    width: 260px;
    padding: 10px 5px;
    margin: 0 10px 0 0;
    border: 1px solid #d0d6d9;
    vertical-align: top;
}

.regist-code-input {
    width: 130px;
}

.regist-btn {
    display: inline-block;
    width: 160px;
    line-height: 40px;
    background-color: #39b94e;
    color: #fff;
    text-align: center;
    text-decoration: none;
}

.regist-btn:hover {
    background-color: #33a646;
}

.icon-warn {
    display: inline-block;
    width: 20px;
    height: 21px;
    background: url(http://img.mukewang.com/5453084a00016ae300120012.gif) no-repeat center;
}

.regist-star {
    position: absolute;
    margin-left: -1em;
    font-family: simsun;
    color: #f30;
}

.regist-remark {
    position: absolute;
    line-height: 21px;
    padding-top: 9px;
    color: #666;
}

.regist-warn {
    padding-left: 20px;
    color: #be3948;
}

.regist-warn > .icon-warn {
    position: absolute;
    margin-left: -20px;
}
```

#### absolute和width/height

*相互替代性*，left/right/top/bottom与width/height很多情况下是可以相互替代的，left/right和top/bottom同时存在时，元素会表现为**双向拉伸**（IE7+）

```html
<div>11111</div>
<div class="box"></div>
<div>22222</div>
```

width/height实现

```css
html, body {
    height: 100%;
    width: 100%;
}
.box {
    position: absolute;
    height: 100%;
    width: 100%;
    background-color: #ccc;
    left: 0;
    top: 0;
    opacity: .7
}
```

left/right/top/bottom实现

```css
html, body {
    height: 100%;
    width: 100%;
}
.box {
    position: absolute;
    background-color: #ccc;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    opacity: .7
}
```

*相互支持性*

- 容器无需固定width/height值，内部元素亦可拉伸

```html
<div class="box">
    <i class="cover"></i>
    <img src="img.jpg" alt="">
</div>
```

```css
.box {
    position: relative;
    /*和float、absolute特性一样，具有包裹性，使容器的尺寸等于内部图片的宽和高*/
    display: inline-block;
}
.box i {
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    opacity: .7;
    background-color: #ccc;
}
```

- 容器拉伸，内部元素支持百分比width/height值

```html
<div class="box">
    <div class="list"><div class="content"></div></div>
    <div class="list"><div class="content"></div></div>
    <div class="list"><div class="content"></div></div>
    <div class="list"><div class="content"></div></div>
    <div class="list"><div class="content"></div></div>
    <div class="list"><div class="content"></div></div>
    <div class="list"><div class="content"></div></div>
    <div class="list"><div class="content"></div></div>
    <div class="list"><div class="content"></div></div>
</div>
```

```css
html, body {
    height: 100%;
    width: 100%;
}
.box {
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
}
.box .list {
    float: left;
    width: 33.3%;
    height: 33.3%;
    position: relative;
}
.box .list .content {
    position: absolute;
    left: 10px;
    right: 10px;
    top: 10px;
    bottom: 10px;
    border-radius: 10px;
    background-color: #cad5eb;
}
```

*相互合作性*

- 当width/height与left/right/top/bottom同时存在时，取width/height的值

```html
<div class="box"></div>
```

```css
html, body {
  height: 100%;
  width: 100%;
}
.box {
  width: 50%;
  height: 50%;
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background-color: #ccc;
}
```

- Left/right与width同时存在，加上margin: auto实现居中

```html
<div class="box">
   <img src="img.jpg" alt="">
</div>
```

```css
html, body {
    height: 100%;
    width: 100%;
}
img {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    width: 50%;
    margin: 0 auto;
}
```

#### 绝对定位与整体布局

- body降级，元素升级

```html
<div class="page">
   <div class="header">
       <div class="title">header</div>
   </div>
   <div class="content">
    <ul>
       <li>1111</li>
       <li>1111</li>
       <li>1111</li>
       <li>1111</li>
       <li>1111</li>
       <li>1111</li>
       <li>1111</li>
       <li>1111</li>
       <li>1111</li>
       <li>1111</li>
       <li>1111</li>
    </ul>
   </div>
   <div class="footer">
       <div class="title">footer</div>
   </div>
</div>
```

```css
html, body {
    height: 100%;
    width: 100%;
}
.page {
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
}
.content {
    position: absolute;
    top: 7%;
    bottom: 7%;
    left: 0;
    right: 0;
    overflow: auto;
}
.content li {
    list-style-type: none;
    width: 100%;
    padding: 20px;
    margin: 10px 0;
    background-color: orange;
}
.header, .footer {
    position: absolute;
    left: 0;
    right: 0;
    height: 7%;
    background-color: #ccc;
    text-align: center;
}
.header .title, .footer .title {
    position: absolute;
    left: 0;
    right: 0;
    bottom: 0;
    top: 0;
    height: 20px;
    margin: auto 0;
}
.header {
    top: 0;
}
.footer {
    bottom: 0;
}
```

#### 参考

[CSS深入理解之absolute](http://www.imooc.com/learn/192)