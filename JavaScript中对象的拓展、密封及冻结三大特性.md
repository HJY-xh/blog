---
title: JavaScript中对象的拓展、密封及冻结三大特性
categories: 技术
tags:
 - JavaScript
date: 2020-03-02 17:24:20
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

## 属性描述符
属性描述符是ES5引入的概念,它用于描述对象的特征.

对象里目前存在的属性描述符有两种主要形式：**数据描述符**和**存取描述符**.

> MDN中的描述：
数据描述符是一个具有值的属性，该值可能是可写的，也可能不是可写的。
存取描述符是由getter-setter函数对描述的属性。
描述符必须是这两种形式之一；不能同时是两者。

数据描述符和存取描述符均具有以下可选键值(默认值是在使用Object.defineProperty()定义属性的情况下)
- `configurable`: **可配置性**.当且仅当该属性的 configurable 为 true 时,该属性描述符才能够被改变,同时该属性也能从对应的对象上被删除.默认为 `false`.
- `enumerable`: **可枚举性**.当且仅当该属性的enumerable为`true`时,该属性才能够出现在对象的枚举属性中.默认为 `false`.

数据描述符同时具有以下可选键值：
- `writable`: **可写性**.当且仅当该属性的writable为true时,value才能被赋值运算符改变.默认为 `false`.
- `value`: **属性值**.该属性对应的值.可以是任何有效的 JavaScript 值(数值,对象,函数等).默认为 `undefined`.

存取描述符同时具有以下可选键值：
- `get`: 一个给属性提供 getter 的方法,如果没有 getter 则为 `undefined`.当访问该属性时.该方法会被执行,方法执行时没有参数传入,但是会传入this对象（由于继承关系,这里的this并不一定是定义该属性的对象）.
- `set`: 一个给属性提供 setter 的方法,如果没有 setter 则为 `undefined`.当属性值修改时,触发执行该方法.该方法将接受唯一参数,即该属性新的参数值.

描述符可同时具有的键值:
|            | configurable |  enumerable | value | writable | get | set
| --------   | -----:  | :----:  | :----:  | :----:  | :----:  | :----:  |
| **数据描述符** | Yes | Yes | Yes | Yes | No | No |
| **存取描述符** | Yes | Yes | No | No | Yes | Yes |

如果一个描述符不具有value,writable,get 和 set 任意一个关键字,那么它将被认为是一个数据描述符.如果一个描述符同时有(value或writable)和(get或set)关键字,将会产生一个异常.

#### 常用API:
>Object.defineProperty(obj, prop, descriptor)


obj:在其上定义或修改属性的对象.

prop:要定义或修改的属性的名称.

descriptor:将被定义或修改的属性描述符.

>Object.defineProperties(obj, props)


obj:在其上定义或修改属性的对象.

props:要定义其可枚举属性或修改的属性描述符的对象.对象中存在的属性描述符主要有两种:数据描述符和访问器描述符

>Object.getOwnPropertyDescriptors(obj)


obj:任意对象.

>Object.getOwnPropertyDescriptor(obj, prop)


obj:需要查找的目标对象.

prop:目标对象内属性名称

#### 用法示例:
```javascript
let obj = {};

Object.defineProperty(obj, 'name', {
    configurable: false,
    enumerable: true,
    writable: true,
    value: 'Cola'
});

Object.defineProperties(obj, {
    color: {
        configurable: false,
        enumerable: true,
        writable: true,
        value: 'red'
    },
    price: {
        configurable: false,
        enumerable: true,
        writable: true,
        value: '3.5'
    }
});

Object.defineProperty(obj, 'updatePrice', {
    configurable: false,
    enumerable: true,
    get: function () {
        return this.capacity;
    },
    set: function (price) {
        this.price = price;
    }
});

console.log(obj);//{ name: 'Cola', color: 'red', price: 3.5, updatePrice: [Getter/Setter] }


obj.updatePrice = 5;
console.log(obj);//{ name: 'Cola', color: 'red', price: 5, updatePrice: [Getter/Setter] }
```

#### 规则：
- 如果属性不可配置,则不能修改它的可配置性和可枚举性,否则抛出异常
```javascript
let obj = {
    name: 'Cola'
}

Object.defineProperty(obj, 'color', {
    configurable: false,
    enumerable: false
})

try {
    Object.defineProperty(obj, 'color', {
        configurable: true
    })
} catch (e) {
    console.log(e); //TypeError: Cannot redefine property: color
}

try {
    Object.defineProperty(obj, 'color', {
        enumerable: true
    })
} catch (e) {
    console.log(e); //TypeError: Cannot redefine property: color
}

console.log(Object.getOwnPropertyDescriptor(obj, 'color'));//{ value: undefined, writable: false, enumerable: false, configurable: false }
```

- 如果存取器属性是不可配置的，则不能修改`get`和`set`方法，也不能将它转换为数据属性
```javascript
let obj = {
    name: 'Cola'
}

Object.defineProperty(obj, 'updateName', {
    configurable: false,
    enumerable: true,
    get: function () {
        return this.name;
    },
    set: function (name) {
        this.name = name;
    }
});

try {
    Object.defineProperty(obj, 'updateName', {
        get: function () {
            return 'Coca ' + this.name;
        }
    });
} catch (e) {
    console.log(e); //TypeError: Cannot redefine property: updateName
}

try {
    Object.defineProperty(obj, 'updateName', {
        set: function (val) {
            this.name = val + this.name;
        }
    });
} catch (e) {
    console.log(e); //TypeError: Cannot redefine property: updateName
}

try {
    Object.defineProperty(obj, 'updateName', {
        value: 'Coco Cola'
    });
} catch (e) {
    console.log(e); //TypeError: Cannot redefine property: updateName
}
```

- 如果数据属性是不可配置的,则不能将它转换为存取器属性;同时也不能将它的可写性从`false`修改成`true`，但可以从`true`修改为`false`
```javascript
let obj = {
    name: 'Cola'
}

Object.defineProperty(obj, 'color', {
    configurable: false,
    writable: false,
    value: 'red'
});

try {
    Object.defineProperty(obj, 'color', {
        writable: true
    });
} catch (e) {
    console.log(e); //TypeError: Cannot redefine property: color
}

try {
    Object.defineProperty(obj, 'color', {
        get: function () {
            return this.name;
        }
    });
} catch (e) {
    console.log(e); //TypeError: Cannot redefine property: color
}

Object.defineProperty(obj, 'price', {
    configurable: false,
    writable: true,
    value: '3.5'
});

try {
    Object.defineProperty(obj, 'price', {
        writable: false,
        value: '5'
    });
} catch (e) {
    console.log(e);
}

try {
    Object.defineProperty(obj, 'price', {
        value: '6'
    });
} catch (e) {
    console.log(e);
}

console.log(Object.getOwnPropertyDescriptor(obj, 'color')); //{ value: 'red', writable: false, enumerable: false, configurable: false }
console.log(Object.getOwnPropertyDescriptor(obj, 'price')); //{ value: '5', writable: false, enumerable: false, configurable: false }
```

- 如果数据属性是不可配置且不可写的,就不能修改它的值;如果是可配置但不可写，则可以修改值
```javascript
let obj = {
    name: 'Cola'
}

Object.defineProperty(obj, 'color', {
    configurable: false,
    writable: false,
    value: 'red'
});

try {
    Object.defineProperty(obj, 'color', {
        value: 'blue'
    });
} catch (e) {
    console.log(e); //TypeError: Cannot redefine property: color
}

Object.defineProperty(obj, 'price', {
    configurable: true,
    writable: false,
    value: '3.5'
});

//可以直接修改值
Object.defineProperty(obj, 'price', {
    configurable: true,
    writable: false,
    value: '12'
});
console.log(obj.price); //5
console.log(Object.getOwnPropertyDescriptor(obj, 'price')); //{ value: '12', writable: false, enumerable: false, configurable: true }

//可以修改writable为true后再修改值
Object.defineProperty(obj, 'price', {
    configurable: true,
    writable: true,
    value: '3.5'
});
obj.price = 5;
console.log(obj.price); //5
```

- 严格模式下,只指定`get`时,如果对该属性赋值将会抛出类型错误异常,只指定`set`时,如果读取该属性将返回undefined,非严格模式下都不抛出异常
```javascript
'use strict'
let obj = {
    name: 'Cola'
}

Object.defineProperty(obj, 'color', {
    get: function () {
        return 'Coca ' + this.name;
    }
});

try {
    obj.color = 'blue';
} catch (e) {
    console.log(e); //TypeError: Cannot set property color of #<Object> which has only a getter
}
console.log(obj.color); //Coca Cola

Object.defineProperty(obj, 'price', {
    set: function (val) {
        this.name = val;
    }
});

try {
    console.log(obj.price);//undefined
} catch (e) {
    console.log(e); 
}
```

## 扩展特性
如果一个对象可以添加新的属性,那么这个对象是可扩展的.如何检验对象是否可扩展及如何让它变得不可扩展呢？

#### 常用API:
>Object.isExtensible(obj)


obj：需要检测的对象

>Object.preventExtensions(obj)


obj：将要变得不可扩展的对象

#### 用法示例:
```javascript
let obj = {
    name: 'Cola'
}
//新创建的对象默认是可扩展的
console.log(Object.isExtensible(obj)); //true

Object.preventExtensions(obj);
console.log(Object.isExtensible(obj)); //false
obj.color = 'red';
console.log(obj); //{ name: 'Cola' }

try {
    Object.defineProperty(obj, "name", {
        value: 'Coca Cola'
    });
    //使用`Object.defineProperty`方法为不可扩展对象添加新属性会抛出异常
    Object.defineProperty(obj, "price", {
        value: 3.5
    });
} catch (e) {
    console.log(e); //TypeError: Cannot define property price, object is not extensible
} finally {
    console.log(obj); //{ name: 'Coca Cola' }
}

console.log(Object.getOwnPropertyDescriptor(obj, 'name'));//{ value: 'Coca Cola', writable: true, enumerable: true, configurable: true }

//writable属性为false时，属性值不可修改
Object.defineProperty(obj, "name", {
    value: 'Coca Cola',
    writable: false,
});
obj.name = 'Sprite';
console.log(obj);

```

## 密封特性
密封对象是指不可扩展，且自身所有属性都不可配置的对象.

也就是说密封对象要满足以下条件:
- 不能添加新属性
- 不能删除已有属性
- 不能修改已有属性的可枚举性、可配置性、可写性,但可能可以修改已有属性的值的对象

#### 常用API:
>Object.isSealed(obj)


obj：需要检测的对象

>Object.seal(obj)

obj：需要被密封的对象

#### 用法示例:
```javascript
let obj = {
    name: 'Cola'
}
//新建的对象默认不是密封的
console.log(Object.isSealed(obj)); //false

//手动密封
Object.preventExtensions(obj);
Object.defineProperty(obj, 'name', {
    configurable: false
})
console.log(Object.isSealed(obj)); //true

//空对象的手动密封
let obj2 = {}
Object.preventExtensions(obj2);
console.log(Object.isSealed(obj2));//true
```

```javascript
let obj = {
    name: 'Cola'
}

Object.seal(obj);
console.log(Object.isSealed(obj)); //true

//密封后不再能够添加或删除属性
obj.color = 'red';
delete obj.name;
console.log(obj); //{ name: 'Coca  Cola' }


//密封后如果writable为true
obj.name = 'Coca Cola';
console.log(obj); //{ name: 'Pepsi  Cola' }
```

## 冻结特性
冻结对象要满足以下条件:
- 不能添加新属性
- 不能删除已有属性
- 不能修改已有属性的可枚举性、可配置性、可写性

即这个对象永远是不可变的.
#### 常用API:
>Object.isFrozen(obj)


obj：需要检测的对象

>Object.freeze(obj)


obj：需要被冻结的对象
#### 用法示例:
```javascript
let obj = {
    name: 'Cola'
}
//新建的对象默认不是冻结的
console.log(Object.isFrozen(obj)); //false

//当对象变得不可扩展且无属性时，也成为冻结对象
Object.preventExtensions(obj);
delete obj.name;
console.log(Object.isFrozen(obj));//true

//不可扩展的空对象是一个密封对象，同时也是冻结对象
console.log(Object.isSealed(Object.preventExtensions({}))); //true
console.log(Object.isFrozen(Object.preventExtensions({}))); //true
```

```
let obj = {
    name: 'Cola'
}
Object.freeze(obj);
console.log(Object.isFrozen(obj)); //true

//对冻结对象的任何操作都会失败
obj.name = 'Coca Cola';
delete obj.name;
console.log(obj);
```
**注意:再严格模式下,对冻结对象的操作会抛出类型异常**
#### 浅冻结
```javascript
let obj = {
    name: 'Cola',
    color: {
        CocaCola: 'red',
        PepsiCola: 'blue'
    }
}
Object.freeze(obj);
obj.name = 'Sprite'
obj.color.CocaCola = 'white';
console.log(obj);//{ name: 'Cola', color: { CocaCola: 'white', PepsiCola: 'blue' } }
```
#### 深冻结
```javascript
function completelyFreezeObj(obj) {
    if (Object.prototype.toString.call(obj) != "[object Object]") {
        console.error("obj不是对象");
        return;
    }
    Object.freeze(obj);
    Object.keys(obj).forEach((key) => {
        if (Object.prototype.toString.call(obj[key]) == "[object Object]") {
            completelyFreezeObj(obj[key]);
        }
    });
};
```
