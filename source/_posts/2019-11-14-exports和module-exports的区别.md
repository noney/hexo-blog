---
title: exports和module.exports的区别
date: 2019-11-14 16:10:29
tags:
     - 日积月累
     - Node.js
---

[bingoo]: https://github.com/noney/bingoo "基于webpack3的vue2构建工具"
[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://noney.coding.me/noney/2017/09/01/webpack终极优化之dll篇/)*

__作者：[Noney Li]__

#### 写在前面
Node.js模块遵循Commandjs规范。Commandjs使用**require**和**exports**进行模块的导入和导出，每个JS文件就是一个模块。在使用模块导出时，经常会看到别人的代码里会出现**exports**或**module.exports**，下面我们来看看他们的区别和用法。
#### 创建测试目录
```
mkdir testExports
cd testExports
touch index.js cal.js
```
#### 打印对象
首先我们先来打印一下**module.exports**和**exports**, cal.js文件如下:
```
console.log(`1、module: ${JSON.stringify(module)}\n`);
console.log(`2、exports: ${JSON.stringify(exports)}\n`);
console.log(`3、module.exports: ${JSON.stringify(module.exports)}\n`);
console.log(`4、是否相待: ${exports === module.exports}\n`);
```
<!-- more -->
命令行执行`node cal.js`，输出如下：
>1、module: {"id":".","path":"/Users/admin/noney-doumi/test/node/sourceRead/testExport","exports":{},"parent":null,"filename":"/Users/admin/noney-doumi/test/node/sourceRead/testExport/cal.js","loaded":false,"children":[],"paths":["/Users/admin/noney-doumi/test/node/sourceRead/testExport/node_modules","/Users/admin/noney-doumi/test/node/sourceRead/node_modules","/Users/admin/noney-doumi/test/node/node_modules","/Users/admin/noney-doumi/test/node_modules","/Users/admin/noney-doumi/node_modules","/Users/admin/node_modules","/Users/node_modules","/node_modules"]}
>2、exports: {}
>3、module.exports: {}
>4、是否相待: true

根据上面的输出结果我们可以得出结论。**exports = module.exports = {}**，也就是说初始化的时候，exports等于module.exports同时指向一个空对象。
#### exports测试
cal.js
```
exports.add = (a, b) => a + b;
exports = {
    add(a, b) {
        return a*b;
    }
}
exports = (a, b) => a + b;
exports = 123;
```
index.js
```
let cal = require('./cal');
console.log(cal);
```
在cal.js中我们分另测试导出函数、对象、基本数据（每次测试一个，注释掉另外代码），在命令行执行`node index.js`，我们得到的输出如下：
>{ add: [Function] }
>{}
>{}
>{}

说明利用这种方式，只有函数是导出成功的，并且它包含在一个对象里。
#### module.exports测试
cal.js
```
module.exports.add = (a, b) => a + b;
module.exports = {
    add(a, b) {
        return a*b;
    }
}
module.exports = (a, b) => a + b;
module.exports = 123;
```
和**exports**一样，我们也是做同样的测试，执行`node index.js`，得到如下输出：
>{ add: [Function] }
>{ add: [Function: add] }
>[Function]
>123

我们可以看到，我们得到了我们所有想要的结果，在这里我们可以看到Node.js模块真正导出的是**module.exports**，而不是*export*。

#### 结论
1、模块初始化的时候，做了如下处理
```
let module = new Module();
let exports = module.export;
```
2、模块导出的时候，真正被导出的是**module.exports**。
3、exports只是module.exports的一个快捷方式在被使用，它不能重新指向新的值，只能向它添加对象属性。
4、当我们需要覆盖导出对象，导出其它类型数据时，就需要使用**module.exports**。
5、我们通常使用**module.exports = exports = add = (a,b ) => a + b**这种方式来导出。