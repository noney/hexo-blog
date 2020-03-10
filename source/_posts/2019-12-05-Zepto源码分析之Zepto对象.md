---
title: Zepto源码分析之Zepto对象
date: 2019-12-05 15:28:37
tags:
     - 读源码
     - Zepto
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面
这篇来分析一下Zepto这个对象上挂载的方法。
#### 源码版本
解读的版本为*1.2.0*
#### 元素与选择器是否匹配
```
var tempParent = document.createElement('div');
/**
 * [matches 元素与选择器是否匹配]
 * @param  {[object]} element  [元素]
 * @param  {[string]} selector [选择器]
 * @return {[boolean]}          [description]
 */
zepto.matches = function(element, selector) {
  // 参数必传，节点必须是元素
  if (!selector || !element || element.nodeType !== 1) return false
  // 检测浏览器是否提供匹配方法
  var matchesSelector = element.matches || element.webkitMatchesSelector ||
                        element.mozMatchesSelector || element.oMatchesSelector ||
                        element.matchesSelector
  // 存在匹配方法，返回匹配结果
  if (matchesSelector) return matchesSelector.call(element, selector)
  // 不存在匹配方法，能过下面方式匹配
  var match, parent = element.parentNode, temp = !parent
  // 不存在父节点，创建一个节点，用做父节点
  if (temp) (parent = tempParent).appendChild(element)
  // 获取元素
  match = ~zepto.qsa(parent, selector).indexOf(element)
  // 清空创建的节点
  temp && tempParent.removeChild(element)
  return match
}
```

通过函数来判断元素与选择器是否匹配，首先检测浏览器是否提供原来匹配方法，如果存在，返回匹配结果，如果不存在。
<!-- more -->
#### 获取元素
```
// 单个选择器正则
var simpleSelectorRE = /^[\w-]*$/
/**
 * [qsa 获取元素]
 * @param  {[object]} element  [元素]
 * @param  {[string]} selector [选择器]
 * @return {[array]}          [元素数组]
 */
zepto.qsa = function(element, selector){
  var found,
      // 是否ID选择器
      maybeID = selector[0] == '#',
      // 是否class选择器
      maybeClass = !maybeID && selector[0] == '.',
      // 去除ID或class选择器第一个符号(#或.)
      nameOnly = maybeID || maybeClass ? selector.slice(1) : selector, // Ensure that a 1 char tag name still gets checked
      // 是否为单个选择器
      isSimple = simpleSelectorRE.test(nameOnly)
      // 单个ID选择器
  return (element.getElementById && isSimple && maybeID) ? // Safari DocumentFragment doesn't have getElementById
    // 使用getElementById获取元素
    ( (found = element.getElementById(nameOnly)) ? [found] : [] ) :
    // 排除节点不是元素、文档、文档片段的元素
    (element.nodeType !== 1 && element.nodeType !== 9 && element.nodeType !== 11) ? [] :
    // 将NodeList转换成数组
    slice.call(
      // 单个非ID选择器
      isSimple && !maybeID && element.getElementsByClassName ? // DocumentFragment doesn't have getElementsByClassName/TagName
        // class选择器，使用getElementsByClassName获取元素
        maybeClass ? element.getElementsByClassName(nameOnly) : // If it's simple, it could be a class
        // 元素选择器，使用getElementsByTagName获取元素
        element.getElementsByTagName(selector) : // Or a tag
        // 其它的情况使用querySelectorAll获取元素
        element.querySelectorAll(selector) // Or it's not simple, and we need to query all
    )
}
```
通过函数获取无素数组，ID通过getElementById、class通过getElementsByClassName、tag通过getElementsByTagName、其它情况使用querySelectorAll获取元素。
这里没有直接通过querySelectorAll来获取元素，是出于性能的考虑。
判断里有element.getElementById、element.getElementsByClassName这样的判断，是因为有的浏览器在有些情况下是不提供相应获取方法的。
#### 处理html片段
```
    // 单个标签正则 <div> <div /> <div></div>
var singleTagRE = /^<(\w+)\s*\/?>(?:<\/\1>|)$/,
    // 带属性的单标签，除去自闭合标签 <div id="test" />
    tagExpanderRE = /<(?!area|br|col|embed|hr|img|input|link|meta|param)(([\w:]+)[^>]*)\/>/ig,
    // 标签开始部分正则 <div>
    fragmentRE = /^\s*<(\w+|!)[^>]*>/,
    table = document.createElement('table'),
    tableRow = document.createElement('tr'),
    // zepto提供了相应的属性方法
    // special attributes that should be get/set via method calls
    methodAttributes = ['val', 'css', 'html', 'text', 'data', 'width', 'height', 'offset']
    containers = {
      'tr': document.createElement('tbody'),
      'tbody': table, 'thead': table, 'tfoot': table,
      'td': tableRow, 'th': tableRow,
      '*': document.createElement('div')
    };
/**
 * [fragment 处理html片段]
 * @param  {[string]} html       [html片段]
 * @param  {[string]} name       [元素名字]
 * @param  {[object]} properties [属性对象]
 * @return {[array]}            [元素对象]
 */
zepto.fragment = function(html, name, properties) {
  var dom, nodes, container
  // 单个标签元素处理
  // A special case optimization for a single tag
  if (singleTagRE.test(html)) dom = $(document.createElement(RegExp.$1))

  if (!dom) {
    // 带属性的单标签，除去自闭合标签修复 <div id="test"></div>
    if (html.replace) html = html.replace(tagExpanderRE, "<$1></$2>")
    // 获取标签tag
    if (name === undefined) name = fragmentRE.test(html) && RegExp.$1
    if (!(name in containers)) name = '*'
    // 根据tag创建元素
    container = containers[name]
    // 把html片段插入到创建的元素
    container.innerHTML = '' + html
    // 删除掉container内的所有子元素，返回无素数组
    dom = $.each(slice.call(container.childNodes), function(){
      container.removeChild(this)
    })
  }
  // 是否纯对象
  if (isPlainObject(properties)) {
    // 元素数组转换为Zepto数组
    nodes = $(dom)
    // 为元素添加属性
    $.each(properties, function(key, value) {
      if (methodAttributes.indexOf(key) > -1) nodes[key](value)
      else nodes.attr(key, value)
    })
  }

  return dom
}
```
通过函数处理html片段，获取所有子节点，为每个节点添加上定义的属性。
#### 实例Z
```
/**
 * [Z 实例Z]
 * @param {[object]} dom      [元素]
 * @param {[string]} selector [选择器]
 */
zepto.Z = function(dom, selector) {
  return new Z(dom, selector)
}
```
#### 是否为zepto.Z实例
```
/**
 * [isZ 是否为zepto.Z实例]
 * @param  {[object]}  object [元素集合]
 * @return {Boolean}        [description]
 */
zepto.isZ = function(object) {
  return object instanceof zepto.Z
}
```
#### 初始化实例
```
// 标签开始部分正则 <div>
var fragmentRE = /^\s*<(\w+|!)[^>]*>/,
/**
 * [init description]
 * @param  {[string]} selector [选择器]
 * @param  {[string]} context  [上下文]
 * @return {[object]}          [description]
 */
zepto.init = function(selector, context) {
  var dom
  // 空，返回一个实例
  // If nothing given, return an empty Zepto collection
  if (!selector) return zepto.Z()
  // 选择器为字符串
  // Optimize for string selectors
  else if (typeof selector == 'string') {
    // 去除前后空格
    selector = selector.trim()
    // 选择器以 < 开头，并且以标签开始，是一个html片段
    // If it's a html fragment, create nodes from it
    // Note: In both Chrome 21 and Firefox 15, DOM error 12
    // is thrown if the fragment doesn't begin with <
    if (selector[0] == '<' && fragmentRE.test(selector))
      // 传入html片段，返回元素数组
      dom = zepto.fragment(selector, RegExp.$1, context), selector = null
    // 如果context存在，创建实例并且执行find
    // If there's a context, create a collection on that context first, and select
    // nodes from there
    else if (context !== undefined) return $(context).find(selector)
    // 如果是一个css选择器，调用zepto.qsa获取元素数组
    // If it's a CSS selector, use it to select nodes.
    else dom = zepto.qsa(document, selector)
  }
  // 选择器为函数，当DOM加载完成的时候调用它
  // If a function is given, call it when the DOM is ready
  else if (isFunction(selector)) return $(document).ready(selector)
  // 选择器为zepto.Z实例，直接返回
  // If a Zepto collection is given, just return it
  else if (zepto.isZ(selector)) return selector
  else {
    // 选择器为数组，过滤掉undefined和null
    // normalize array if an array of nodes is given
    if (isArray(selector)) dom = compact(selector)
    // 选择器为对象，包裹为数组
    // Wrap DOM nodes.
    else if (isObject(selector))
      dom = [selector], selector = null
    // 选择器为html片段，转换获取元素
    // If it's a html fragment, create nodes from it
    else if (fragmentRE.test(selector))
      dom = zepto.fragment(selector.trim(), RegExp.$1, context), selector = null
    // 如果context存在，创建实例并且执行find
    // If there's a context, create a collection on that context first, and select
    // nodes from there
    else if (context !== undefined) return $(context).find(selector)
    // 如果是一个css选择器，调用zepto.qsa获取元素数组
    // And last but no least, if it's a CSS selector, use it to select nodes.
    else dom = zepto.qsa(document, selector)
  }
  // 创建一个Zepto实例
  // create a new Zepto collection from the nodes found
  return zepto.Z(dom, selector)
}
```
通过函数初始化实例，根据选择器类型做如下操作：

| 类型 | 处理 | 例子 |
| :----| :----| :---- |
| **空** | 返回一个空的实例 | `$()` |
| **字符串** 以 < 开头，并且以标签开始 | 调用zepto.fragment，返回元素实例数组 | `$("<div>test</div>")` `$("<div>test</div>", document)` |
| **字符串** context存在 | 创建实例并且执行find | `$("#test", document)`  |
| **字符串** css选择器 | 调用zepto.qsa获取元素数组实例 | `$("#test")`  |
| **函数** | 调用$(document).ready | `$(() => {})`  |
| **zepto.Z实例** | 直接返回 | `$('li').each((v, k) => $(v))`  |
| **数组** | 过滤掉undefined和null | `$(<DOM nodes>)`  |
| **对象** | 包裹为数组 | `$(<DOM nodes>)`  |
| **其它情况** html片段| 调用zepto.fragment，返回元素实例数组 | 异步返回html片段  |
| **其它情况** context存在| 创建实例并且执行find | &nbsp; |
| **其它情况** css选择器| 调用zepto.qsa获取元素数组实例 | &nbsp; |

#### 参考
[读Zepto源码之神奇的$](https://github.com/yeyuqiudeng/reading-zepto/blob/master/src/%E8%AF%BBZepto%E6%BA%90%E7%A0%81%E4%B9%8B%E7%A5%9E%E5%A5%87%E7%9A%84%24.md)