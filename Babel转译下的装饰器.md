---
title: Babel转译下的装饰器
categories: 技术
tags:
 - JavaScript
date: 2020-08-16 19:40:50
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

#### 装饰器概念
它是一个函数，它会通过返回一个新函数来修改传入的函数或方法的行为

#### 装饰器用法
- 装饰类方法或属性(类成员)
- 装饰类

```javascript
const log = (target, name, descriptor) => {
  let oldValue = descriptor.value;
  if (typeof descriptor.value === "function") {
    descriptor.value = function (...args) {
      return console.log(oldValue.apply(this, args));
    }
  } else {
    console.log(descriptor.value)
  }
}

const foodCategory = (target, name, descriptor) => {
  target.category = "food";
}

@foodCategory
class Cola {
  constructor(name) {
    this.name = name;
  }

  @log
  color() {
    return `red`;
  }
}

let cola = new Cola("Coca Cola");
cola.color(); // red
console.log(Cola.category); // food
```

**装饰方法本质上是通过 `Object.defineProperty()` 来实现的**

经过babel转换之后(底下有babel详细操作):
```javascript
"use strict";

/**
 * @function 
 * 该函数的作用就是将数组中的方法添加到构造函数或者构造函数的原型中
 * 最后返回这个构造函数。
 */
var _createClass = function () {
    /**
     * 将props数组上的每一个对象都通过Object.defineProperty()方法
     * 定义到目标对象target上去
     */
    function defineProperties(target, props) {
        for (var i = 0; i <
            props.length; i++) {
            var descriptor = props[i];
            // 类的内部所有定义的方法,都是不可枚举的
            descriptor.enumerable = descriptor.enumerable || false;
            descriptor.configurable = true;
            if ("value" in descriptor) descriptor.writable = true;
            Object.defineProperty(target, descriptor.key, descriptor);
        }
    }
    /**
     * @param {protoProps} 原型属性数组
     * @param {protoProps} 静态属性数组
     */
    return function (Constructor, protoProps, staticProps) {
        // 为构造函数prototype添加属性
        // (即为用构造函数生成的实例原型添加属性,可以被实例通过原型链访问到)
        if (protoProps) defineProperties(Constructor.prototype, protoProps);
        // 为构造函数添加属性
        if (staticProps) defineProperties(Constructor, staticProps);
        return Constructor;
    };
}();

var _class, _desc, _value, _class2;

/**
 * @function 
 * 作用是检查 Person 是否是通过 new 的方式调用
 * 防止构造函数被当做普通函数执行
 */
function _classCallCheck(instance, Constructor) {
    if (!(instance instanceof Constructor)) {
        throw new TypeError("Cannot call a class as a function");
    }
}

function _applyDecoratedDescriptor(target, property, decorators, descriptor, context) {
    var desc = {};
    // 这里对 descriptor 属性做了一层拷贝
    Object['keys'](descriptor).forEach(function (key) {
        desc[key] = descriptor[key];
    });
    desc.enumerable = !!desc.enumerable;
    desc.configurable = !!desc.configurable;

    // 没有 value 或者 initializer 属性的,表明是 get 和 set 方法
    // initializer是 Babel 的 Class 为了与 decorator 配合而产生的一个属性
    if ('value' in desc || desc.initializer) {
        desc.writable = true;
    }

    // 这里处理多个 decorator 的情况,由类内向类外执行
    desc = decorators.slice().reverse().reduce(function (desc, decorator) {
        return decorator(target, property, desc) || desc;
    }, desc);

    // void其实是javascript中的一个函数,接受一个参数,返回值永远是undefined
    if (context && desc.initializer !== void 0) {
        desc.value = desc.initializer ? desc.initializer.call(context) : void 0;
        desc.initializer = undefined;
    }

    if (desc.initializer === void 0) {
        Object['defineProperty'](target, property, desc);
        desc = null;
    }

    return desc;
}

var log = function log(target, name, descriptor) {
    var oldValue = descriptor.value;
    if (typeof descriptor.value === "function") {
        descriptor.value = function () {
            for (var _len = arguments.length, args = Array(_len), _key = 0; _key < _len; _key++) {
                args[_key] = arguments[_key];
            }

            return console.log(oldValue.apply(this, args));
        };
    } else {
        console.log(descriptor.value);
    }
};

var foodCategory = function foodCategory(target, name, descriptor) {
    target.category = "food";
};

var Cola = foodCategory(_class = (_class2 = function () {
    function Cola(name) {
        _classCallCheck(this, Cola);

        this.name = name;
    }

    _createClass(Cola, [{
        key: "color",
        value: function color() {
            return "red";
        }
    }]);

    return Cola;
}(), (_applyDecoratedDescriptor(_class2.prototype, "color", [log], Object.getOwnPropertyDescriptor(_class2.prototype, "color"), _class2.prototype)), _class2)) || _class;

var cola = new Cola("Coca Cola");
cola.color();
console.log(Cola.category);
```


#### babel详细操作
使用`npm init`初始化项目:
生成文件`pakeage.json`,它会将记录项目开发中所要用到的包,以及项目的详细信息

babel-cli是一种在命令行下使用Babel编译文件的简单方法,主要用于文件的输入输出
安装babel命令行工具:
```javascript
npm install --global babel-cli
```

安装装饰器依赖:
```javascript
npm i --save-dev babel-plugin-transform-decorators-legacy
```

项目中创建**.babelrc**文件:
```javascript
{
    "presets": [
      "es2015"
    ],
    "plugins": [
        "transform-decorators-legacy"
    ]
  }
```
文件说明:
- .babelrc
babel所有的操作基本都会来读取这个配置文件,除了一些在回调函数中设置options参数的,如果没有这个配置文件,会从package.json文件的babel属性中读取配置

- presets
可以简单的把它视为 Babel Plugin 的集合

- plugins
babel中的插件,通过配置不同的插件告诉babel,代码中有哪些是需要转译的


使用babel命令转码:
```javascript
babel [fileName].js
```



