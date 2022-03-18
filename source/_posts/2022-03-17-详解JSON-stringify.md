---
title: 详解JSON.stringify
date: 2022-03-17 15:57:00
tags:
---

[Noney Li]: https://github.com/noney/ "noneyli"

*—— 同时发表于[coding](http://0kv30q.coding-pages.com/)*

__作者：[Noney Li]__

#### 写在前面
`JSON`对象的`stringify`方法用于将`Javascript`**对象**或**值**转换为`JSON`字符串。

```javascript
JSON.stringify(value[, replacer[, space]])
```

#### 参数

- value **必填** 将要序列化成`JSON`字符串的值，可以是对象或值；
```javascript
  // 对象
  console.log(JSON.stringify({ name: 'noneyli', sex: '男' })) // '{"name":"noneyli","sex":"男"}'
  // 值
  console.log(JSON.stringify('中午吃啥')) // "中午吃啥"
  console.log(JSON.stringify(1)) // "1"
  console.log(JSON.stringify(true)) // "true"
  console.log(JSON.stringify(null)) // "null"
  console.log(JSON.stringify(undefined)) // "undefined"
  console.log(JSON.stringify(NaN)) // "null"
  console.log(JSON.stringify(Symbol())) // "undefined"
```

<!-- more -->

- replacer **可选**
  > 参数为`函数`时，属性依次被函数处理，返回值为最终属性值，返回值为`undefined`时，该属性被排除；

```Javascript
  console.log(JSON.stringify({ name: 'noneyli', sex: '男', age: 100 }, (key, value) => {
    return typeof value === 'number' ? undefined : value
  }))
  // '{"name":"noneyli","sex":"男"}'
```
  > 参数为`数组`时，数组中的值为输出对象的属性名；

```Javascript
  console.log(JSON.stringify({ name: 'noneyli', sex: '男', age: 100 }, [ 'name' ])) // '{"name":"noneyli"}'
```
  > 参数为`null`或省略时，所有属性将被序列化。

- space **可选** 指定缩进用的空白字符串，用于美化输出
  > 参数为`数值`是，表示属性名前添加空格的个数。小于**1**时，表示没有空格。最多为**10**个空格;

```Javascript
  console.log(JSON.stringify({ name: 'noneyli', sex: '男', age: 100 }, null, 2))
  /*
  {
    "name": "noneyli",
    "sex": "男",
    "age": 100
  }
  */
```
  > 参数为`字符串`时，表示属性名前添加该字符串。长度大于**10**时，取前**10**个字符
  > 参数为`null`或省略时，表示没有空格。

```Javascript
  console.log(JSON.stringify({ name: 'noneyli', sex: '男', age: 100 })) // '{"name":"noneyli","sex":"男","age":100}'
```

#### 异常

- 当存在循环引用时，会抛出异常`cyclic object value`;
- 当尝试转换`BigInt`时，会抛出异常`"BigInt value can't be serialized in JSON`。

#### 9大特性

- 特性一

> `undefined`、`函数`、`symbol值`出现在**非数组**中时，会被**忽略**

```Javascript
  const obj = {
    name: 'noneyli',
    sex: '男',
    age: undefined,
    say() {
      console.log('hello');
    },
    symbolHeight: Symbol()
  }

  console.log(JSON.stringify(obj)); // {"name":"noneyli","sex":"男"}
```
> `undefined`、`函数`、`symbol值`出现在**数组**中时，会被**转换为null**

```Javascript
  const obj = {
    name: 'noneyli',
    sex: '男',
    arr: [undefined, function() {}, Symbol()]
  }

  console.log(JSON.stringify(obj)); // {"name":"noneyli","sex":"男","arr":[null,null,null]}
```
> `undefined`、`函数`、`symbol值`被单独转换时，会返回**undefined**

```Javascript
  console.log(JSON.stringify(undefined)); // undefined
  console.log(JSON.stringify(function() {})); // undefined
  console.log(JSON.stringify(Symbol())); // undefined
```

- 特性二 `boolean`、`number`、`string`的**包装对象**会序列化过程中自动转换成对应的**原始值**

```Javascript
  console.log(JSON.stringify(new Boolean(false))); // false
  console.log(JSON.stringify(new Number(7))); // 7
  console.log(JSON.stringify(new String('noneyli'))); // noneyli
```

- 特性三 当`Symbol`出现在属性值、属性键时都会被**忽略**

```Javascript
  const obj = {
    [Symbol()]: 'noneyli',
    age: Symbol()
  }

  console.log(JSON.stringify(obj)); // {}
```

- 特性四 属性值为`null`、`NaN`、`Infinity`时会被转换为**null**

```Javascript
  const obj = {
    name: null,
    sex: NaN,
    age: Infinity
  };

  console.log(JSON.stringify(obj)); // {"name":null,"sex":null,"age":null}
```

- 特性五 转换值如果有`toJSON`方法时，将序列化其执行结果

```Javascript
  const obj = {
    name: null,
    sex: NaN,
    age: Infinity,
    toJSON() {
      return 'noneyli';
    }
  };

  console.log(JSON.stringify(obj)); // noneyli
```

- 特性六 `Date`日期会序列化时，会调用它的`toJSON`方法

```Javascript
  console.log(new Date().toJSON()); // 2022-03-18T07:16:35.407Z
  console.log(JSON.stringify(new Date())); // 2022-03-18T07:16:35.407Z
```

- 特性七 对循环引用的对象，会抛出**错误**

```Javascript
  const obj = {
    name: 'noneyli'
  };

  obj.age = obj;

  console.log(JSON.stringify(obj)); // TypeError: Converting circular structure to JSON
```

- 特性八 序列化**对象**时，只会序列**可枚举**的属性

```Javascript
  const obj = {};

  Object.defineProperties(obj, {
    name: {
      value: 'noneyli',
      enumerable: true
    },
    sex: {
      value: '男',
      enumerable: false
    },
  })

  console.log(JSON.stringify(obj)); // {"name":"noneyli"}
```

- 特性九 序列化`BigInt`类型的值，会抛出**错误**

```Javascript
  const alsoHuge = BigInt(9007199254740991)

  console.log(JSON.stringify(alsoHuge)) // TypeError: Do not know how to serialize a BigInt
```

#### 参考

[就因为JSON.stringify，我的年终奖差点打水漂了](https://mp.weixin.qq.com/s/5Ik0LXw5MOhVACWpegDCxA)
