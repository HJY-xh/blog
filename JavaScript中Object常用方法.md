---
title: JavaScript中Object常用方法
categories: 技术
tags:
 - JavaScript
date: 2020-04-30 22:34:24
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

## 写在前面
本篇文章尚未完成!!
## 概述
`Object`是`JavaScript`中标准内置对象，它非常强大，虽然在日常开发中却用的不多，但十分有必要深入学习。

### Object.create()
该方法创建一个新对象，使用现有的对象提供新创建对象的__proto__.
`Object.create(proto[, propertiesObject])`
参数：
- proto为新创建对象的原型对象
- propertiesObject可选，如果没有指定为undefined，这样会添加到新创建对象的不可枚举（默认）属性（即其自身定义的属性，而不是其原型链上的枚举属性）对象的属性描述符以及响应的属性名称。
返回值：
- 一个新对象，带着指定原型对象和属性


### Object.defineProperty()
该方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。

先看看常用的对象定义属性并赋值的写法：密码，，，，，，，，，，，，，，，，，，。xcccccccccccccccccc.mpo[yup]
```javascript
let obj = {};
obj.name = 'Cola';
console.log(obj);// { name: 'Cola' }
````
这种方式简单粗暴，但是对象属性的值可以想改就改，想删就删：
```javascript
let obj = {};
obj.name = 'Cola';
console.log(obj);// { name: 'Cola' }

obj.name = 'Coca cola';
console.log(obj);// { name: 'Coca cola' }

delete obj.name;
console.log(obj);// {}
```

再来看看`Object.defineProperty(obj, prop, descriptor)`
参数：
- obj为要定义属性的对象
- prop为要定义或修改的属性的名称或Symbol
- descriptor为要定义或修改的属性描述符
返回值：
- 被传递给函数的对象
**ES6新增Symbol类型，，由于它独一无二的特性，可以用该类型作为对象的key**

```javascript
let obj = {};
Object.defineProperty(obj, 'name', {
    value: 'Cola'
})
console.log(obj);// {}
console.log(obj.name);// Cola

obj.name = 'Coca cola';
console.log(obj.name);// Cola

delete obj.name;
console.log(obj.name);// Cola
```
通过实践发现以这样的写法定义的属性是无法被修改和删除的，且访问对象为空。
要想和我们常规写法达到相同的效果，还需要修改属性描述符。
对象里目前存在的属性描述符有两种主要形式：**数据描述符**和**存取描述符**，用一张表来区分二者区别：
|   |configurable   |enumerable   |value   |writable   |get   |set   |
| ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ |
| 数据描述符  |√   |√   |√   |√   |×   |×   |
| 存取描述符  |√   |√   |×   |×   |√   |√   |
如果一个描述符不具备`value`、`enumerable`、`writable`、`configurable`任意一个键，那么它会被认为是一个数据描述符；
如果一个描述符同时拥有`value`或`writable`和`get`、`set`，则会产生一个异常。

下面再来看一个示例：
```javascript
let obj = {};
Object.defineProperty(obj, 'name', {
    enumerable: true,
    configurable: true,
    writable: true,
    value: 'Cola'
})
console.log(obj);// { name: 'Cola' }
console.log(obj.name);// Cola

obj.name = 'Coca cola';
console.log(obj.name);// Coca cola

delete obj.name;
console.log(obj.name);// undefined
```
可以看到obj对象和我们以常用写法达到的效果一样，这是因为`enumerable`描述符能够控制对象属性是否可枚举;`writable`描述符能够控制对象属性是否可写，也就是覆盖;`configurable`描述符能够控制对象属性是否可配置，也就是将属性从对象上删除。
这里我们可以得出结论：除了`value`描述符，其他数据描述符的值默认为`false`。

那`get`和`set`呢，再看看以下的例子：
```javascript
let obj = {};
Object.defineProperty(obj, 'name', {
    enumerable: true,
    configurable: true,
    writable: true,
    value: 'Cola',
    get() {},
    set() {}
})
// TypeError: Invalid property descriptor. Cannot both specify accessors and a value or writable attribute 
```
对照前文的表格，代码抛出异常，证实了`value`或`writable`和`get`、`set`不能同时出现。
那怎么使用呢，按照异常提示信息，删掉`value`和`writable`描述符：
```javascript
let obj = {};
Object.defineProperty(obj, 'name', {
    enumerable: true,
    configurable: true,
    get() {
        console.log('get!');
        // return obj;
    },
    set(val) {
        console.log(val);
    }
})
console.log(obj);// { name: [Getter/Setter] }
obj.name;// get!
obj.name = 'Coca cola';// Coca cola
```
在上面代码中，每次访问`obj.name`总是返回同一个值，这是因为我们对这个属性做了特殊处理，也就是get方法做的事情；每次对`obj.name`进行赋值操作，控制台总是打印此次赋值的内容，也就是set方法做的事情。

### Object.assign()
该方法是ES6新添加的方法，用于将所有可枚举属性的值从一个或多个源对象复制到目标对象，返回目标对象。
`Object.assign(target, ...sources)`
参数：
- target为目标对象
- sources为源对象
返回值：
- 目标对象
用法示例：
```javascript
let manA = {
    name: 'manA',
    age: 23
}
Object.defineProperties(manA, {
    'address': {
        enumerable: true,
        value: 'China'
    },
    'height': {
        value: '1.8m'
    }
})

let manB = {
    name: 'manB',
    age: 13
}

let manC = Object.assign({}, manA, manB, null, undefined);
console.log(manC);// { name: 'manB', age: 13, address: 'China' }
```
实践后可以得出结论：
- 该方法只拷贝源对象自身的并且可枚举的属性到目标对象（其内部使用源对象存取描述符`get()`和目标对象的`set()`方法，因此它会调用相关方法）。
- 该方法不会因为sources参数为`null`或`undefined`而报错。

深拷贝问题：
`Object.assign()`方法只能实现浅拷贝：假如源对象的属性值是一个对象的引用，那么返回值中该属性值也直指向这个引用
```javascript
let manA = {
    name: 'manA',
    address: {
        region: 'Asia',
        nationality: 'China'
    }
}

let manB = Object.assign({}, manA);
console.log(manB);// { name: 'manA', address: { region: 'Asia', nationality: 'China' } }
manB.address.nationality = 'Japan';
console.log(manA)；// { name: 'manA', address: { region: 'Asia', nationality: 'Japan' } }
```