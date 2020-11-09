---
title: JavaScript中深拷贝和浅拷贝
categories: 技术
tags:
 - JavaScript
date: 2020-02-05 19:07:22
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

在JavaScript中,对于引用类型数据的值,当从一个变量向另一个变量复制引用类型值时，这个值的副本其实是一个指针，两个变量指向同一个堆对象，改变其中一个变量，另一个也会受到影响.
这种拷贝分为两种情况:拷贝引用和拷贝实例,也就是我们说的浅拷贝和深拷贝.
## 浅拷贝
- 浅拷贝是拷贝一层，深层次的对象级别的就拷贝引用:
第一个例子是直接拷贝原对象的引用
```javascript
	let obj = { name: "Cola", detail: { price: 3.5 }, color: ['red'] };
	let obj2 = obj;
	obj2.name = "Coca Cola";
	obj2.detail.price = 5;
	obj2.color[0] = 'blue';

	console.log(obj)//{ name: 'Coca Cola', detail: { price: 5 }, color: [ 'blue' ] }
	console.log(obj2)//{ name: 'Coca Cola', detail: { price: 5 }, color: [ 'blue' ] }
```
再看第二个例子,理解拷贝一层的含义(Object.assign()方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。注意：Object.assign()拷贝的是属性值，假如源对象的属性值是一个指向对象的引用，它也只拷贝那个引用值)
```javascript
	let obj = { name: 'Cola', detail: { color: 'red' } };
	let obj2 = Object.assign({}, obj);
	obj2.name='Coca Cola';
	obj2.detail.color='blue';

	console.log(obj);//{ name: 'Cola', detail: { color: 'blue' } }
	console.log(obj2);//{ name: 'Coca Cola', detail: { color: 'blue' } }
```

- 其他实现方法
	- 扩展运算符
```javascript
	let obj = { name: 'Cola', detail: { color: 'red' } };
	let obj2 = {...obj};
	obj2.name='Coca Cola';
	obj2.detail.color='blue';

	console.log(obj);//{ name: 'Cola', detail: { color: 'blue' } }
	console.log(obj2);//{ name: 'Coca Cola', detail: { color: 'blue' } }
```

	- Array.prototype.slice(),该方法提取并返回一个新的数组,如果源数组中的元素是个对象的引用,slice会拷贝这个对象的引用到新的数组
```javascript
	let arr = ['Cola', { color: 'red' }];
	let arr2 = arr.slice();
	arr2[0] = 'Coca Cola';
	arr2[1].color = 'blue';

	console.log(arr);//[ 'Cola', { color: 'blue' } ]
	console.log(arr2);//[ 'Coca Cola', { color: 'blue' } ]
```
	
	- Array.prototype.concat(),该方法用于合并多个数组,并返回一个新的数组,和slice方法类似,当源数组中的元素是个对象的引用，concat在合并时拷贝的就是这个对象的引用
```javascript
	let arr = ['Cola', { color: 'red' }];
	let arr2 = [{ price: 3.5 }];
	let arr3 = arr.concat(arr2);
	arr3[0] = 'Coca Cola';
	arr3[2].price = 5;

	console.log(arr);//[ 'Cola', { color: 'red' } ]
	console.log(arr2);//[ { price: 5 } ]
```

- 手动实现浅拷贝:
```javascript
	function shallowClone(source) {
		if (!source || typeof source !== 'object') {
			throw new Error('error arguments');
		}
		var target = source.constructor === Array ? [] : {};
		for (var key in source) {
			if (source.hasOwnProperty(key)) {
				target[key] = source[key];
			}
		}
		return target;
	}

	let obj = ['Cola', { color: 'red' }];
	let obj2 = shallowClone(obj);
	
	obj[0] = 'Coca Cola'
	obj[1].color = 'blue'
	
	console.log(obj)//[ 'Coca Cola', { color: 'blue' } ]
	console.log(obj2)//[ 'Cola', { color: 'blue' } ]
```

## 深拷贝
- 深拷贝是拷贝多层，每一级别的数据都会拷贝出来:也就是说深拷贝会另外拷贝一份一个一模一样的对象,从堆内存中开辟一个新的区域存放新对象,新对象跟原对象不共享内存，修改新对象不会改到原对象.
-常用方法:JSON.parse(JSON.stringify())
```javascript
let obj = { name: 'Cola', detail: { color: 'red' } };
let obj2 = JSON.parse(JSON.stringify(obj));
obj2.name = 'Coca Cola';
obj2.detail.color = 'blue'
console.log(obj);//{ name: 'Cola', detail: { color: 'red' } }
console.log(obj2);//{ name: 'Coca Cola', detail: { color: 'blue' } }
```
这是前端开发过程中比较常用的深拷贝方式。原理是把一个对象序列化成为一个JSON字符串，将对象的内容转换成字符串的形式再保存在磁盘上，再用JSON.parse()反序列化将JSON字符串变成一个新的对象.
它有一些值得注意的地方
	- 拷贝的对象的值中如果有函数,undefined,symbol则经过JSON.stringify()序列化后的JSON字符串中这个键值对会消失
	- 对象中含有NaN、Infinity和-Infinity，则序列化的结果会变成null

- 还可以借助jQuery，lodash等第三方库完成一个深拷贝实例.

- 手动实现深拷贝
```javascript
	function deepClone(source) {
		if (!source || typeof source !== 'object') {
			throw new Error('error arguments');
		}
		var target = source.constructor === Array ? [] : {};
		for (var key in source) {
			if (source.hasOwnProperty(key)) {
				if (typeof source[key] === 'object' && source[key]) {
					target[key] = deepClone(source[key]);
				} else {
					target[key] = source[key];
				}
			}
		}
		return target;
	}

	let obj = { name: 'Cola', detail: { color: 'red' } };
	let obj2 = deepClone(obj);
	obj2.name='Coca Cola';
	obj2.detail.color = 'blue';

	console.log(obj) // { name: 'Cola', detail: { color: 'red' } }
	console.log(obj2) // { name: 'Coca Cola', detail: { color: 'blue' } }
```
