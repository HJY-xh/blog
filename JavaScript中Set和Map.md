---
title: JavaScript中Set和Map
categories: 技术
tags:
 - JavaScript
date: 2020-02-07 15:32:12
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

## Set
ES6 提供了新的数据结构 Set.它类似于数组,但是成员的值都是唯一的,没有重复的值.

Set 本身是一个构造函数,用来生成 Set 数据结构.
基本用法:
```javascript
let set = new Set([1,1,2,3,4,4]);
console.log(set);//Set { 1, 2, 3, 4 }
console.log(set.size);//4
```
接下来详细了解一下Set实例的属性和方法:
- 属性
	- size:返回集合所包含元素的数量
- 方法
	- 操作方法(见例子1)
		- add(value):向集合中添加一个新的项
		- delete(value):从集合中移除一个值
		- has(value): 判断一个值在集合中是否存在,存在返回true,否则false
		- clear(): 移除集合里所有的项
	- 遍历方法(见例子2)
		- keys():返回键名的遍历器
		- values():返回键值的遍历器
		- entries():返回键值对的遍历器
		- forEach():使用回调函数遍历每个成员

例子1:
```javascript
let set = new Set();

set.add('Cola');
set.add(3.5);
set.add('red');
console.log(set);//Set { 'Cola', 3.5, 'red' }

set.delete(3.5);
console.log(set);//Set { 'Cola', 'red' }

console.log(set.has('red'));//true
console.log(set.has('blue'));//false

set.clear();
console.log(set);//Set {}
```

例子2:
```javascript
let set = new Set(['Cola', 3.5, 'red']);
console.log(set.keys());//SetIterator { 'Cola', 3.5, 'red' }
console.log(set.values());//SetIterator { 'Cola', 3.5, 'red' }

console.log(set.entries());//SetIterator { [ 'Cola', 'Cola' ], [ 3.5, 3.5 ], [ 'red', 'red' ] }
set.forEach((value, key, array) => {
  console.log(key + ' : ' + value + ' : ' + array);
})
//Cola : Cola : [object Set]
//3.5 : 3.5 : [object Set]
//red : red : [object Set]
```
**注意:Set的遍历顺序就是插入顺序,由于 Set 结构没有键名,只有键值(或者说键名和键值是同一个值),所以keys方法和values方法的行为完全一致**

for of遍历方式:
```javascript
let set = new Set(['Cola', 3.5, 'red']);
for(let value of set){
  console.log(value);
}
//Cola
//3.5
//red

for(let [value,key] of set.entries()){
  console.log(value+':'+key);
}
//Cola:Cola
//3.5:3.5
//red:red
```

#### Set和Array转换
- 数组 => Set:
```javascript
let arr = ['Cola', 3.5, 'red'];
let set = new Set(arr);
console.log(set);//Set { 'Cola', 3.5, 'red' }
```

- Set => 数组:
```javascript
let set = new Set(['Cola', 3.5, 'red']);
let arr = Array.from(set)
console.log(arr);//[ 'Cola', 3.5, 'red' ]
```

## WeakSet
我们再看看Set,当有对象存储在Set实例中时,这相当于把对象存储在变量中,只要Set实例的引用仍然存在,所存储的对象就无法被垃圾回收机制回收,从而无法释放内存:
```javascript
let set = new Set();
let obj = {name:'Cola'};
let color = 'red';
set.add(obj);
set.add(color);
console.log(set.size); // 2

obj = null;
color = 'blue';
console.log(set.size); // 2
console.log(obj); // null
console.log(set); // Set { { name: 'Cola' }, 'red' }
```

WeakSet 结构与 Set 类似,也是不重复的值的集合.但它与 Set 有两个区别:
- WeakSet 的成员只能是对象，而不能是其他类型的值.
```javascript
let ws = new WeakSet();
ws.add('Cala');//TypeError: Invalid value used in weak set
```

- WeakSet 中的对象都是弱引用,即垃圾回收机制不考虑 WeakSet 对该对象的引用,也就是说,如果其他对象都不再引用该对象,那么垃圾回收机制会自动回收该对象所占用的内存,不考虑该对象还存在于 WeakSet 之中.因为这个特点,WeakSet 不可遍历.
```javascript
let ws = new WeakSet();
let obj ={name:'Cola'}
ws.add(obj);
ws.add({});
console.log(ws.has(obj))//true

ws.delete(obj)
console.log(ws.has(obj))//false
```
- Weak Set 不提供任何迭代器(例如 keys() 与 values() 方法),没有size属性.

## Map
Map类型是一组键值对的结构,具有极快的查找速度.它是键值对的有序列表，而键和值都可以是任意类型.
基本用法:
```javascript
let map = new Map([['type','food'],['name', 'Cola']]);
map.set('color', 'red').set('price', 3.5);
console.log(map.size)//4
console.log(map.get('name'))//Cola
console.log(map.get('size'))//undefined
console.log(map);//Map { 'type' => 'food', 'name' => 'Cola', 'color' => 'red', 'price' => 3.5 }
```
接下来详细了解一下Set实例的属性和方法:
- 属性
	- size:返回集合所包含元素的数量
- 方法
	- 操作方法(见例子3)
		- set(key, value):设置键名key对应的键值为value,然后返回整个 Map 结构.如果key已经有值,则键值会被更新,否则就新生成该键.set方法返回的是当前的Map对象，因此可以采用链式写法
		- get(key):读取key对应的键值，如果找不到key，返回undefined
		- has(key):判断指定键在Map中是否存在,存在返回true,否则false
		- delete(ley):移除Map中的建以及对应的值
		- clear():移除Map中所有的键与值
	- 遍历方法(见例子4)
		- keys():返回键名的遍历器
		- values():返回键值的遍历器
		- entries():返回所有成员的遍历器
		- forEach():遍历 Map 的所有成员
		

例子3:
```javascript
let map = new Map()
map.set('name', 'Cola');
map.set('price', 3.5);

console.log(map.has('name'))//true
console.log(map.has('color'))//false

map.delete('name');
console.log(map.has('name'))//false

map.clear();
console.log(map.size)//0
```

例子4:
```javascript
let map = new Map([['type', 'food'], ['name', 'Cola'], ['color', 'red'], ['price', 3.5]]);
console.log(map.keys());//MapIterator { 'type', 'name', 'color', 'price' }
console.log(map.values());//MapIterator { 'food', 'Cola', 'red', 3.5 }
console.log(map.entries());
//MapIterator {
//  [ 'type', 'food' ],
//  [ 'name', 'Cola' ],
//  [ 'color', 'red' ],
//  [ 'price', 3.5 ] }

map.forEach((value, key, map) => {
  console.log(key + ':' + value)
})
//type:food
//name:Cola
//color:red
//price:3.5
```

for of遍历方式:
```javascript
let map = new Map([['type', 'food'], ['name', 'Cola'], ['color', 'red'], ['price', 3.5]]);
for (let item of map.entries()) {
  console.log(item[0] + ':' + item[1])
}
//type:food
//name:Cola
//color:red
//price:3.5
```

#### Map与其他数据结构的相互转换
- Map => 数组
```javascript
	let map = new Map([['type', 'food'], ['name', 'Cola'], ['color', 'red'], ['price', 3.5]]);
	let arr = [...map];//或者 let arr = Array.from(map);
	console.log(arr);//[ [ 'type', 'food' ],[ 'name', 'Cola' ],[ 'color', 'red' ],[ 'price', 3.5 ] ]
```
- 数组 => Map
```javascript
	let map = new Map([['type', 'food'], ['name', 'Cola'], ['color', 'red'], ['price', 3.5]]);
```
- Map => 对象
```javascript
	let map = new Map([['type', 'food'], ['name', 'Cola'], [2, 'red']]);

	function mapToObj(strMap) {
	  let obj = Object.create(null);
	  for (let [k, v] of strMap) {
		obj[k] = v;
	  }
	  return strMap;
	}

	console.log(mapToObj(map))//Map { 'type' => 'food', 'name' => 'Cola', 2 => 'red' }
```
- 对象 => Map
```javascript
	obj = { name: 'Cola', color: 'red' };

	function objToMap(obj) {
	  let map = new Map();
	  for (let item of Object.keys(obj)) {
		map.set(item, obj[item]);
	  }
	  return map;
	}

	console.log(objToMap(obj));//Map { 'name' => 'Cola', 'color' => 'red' }
```

## WeakSet
如果希望不再引用Map的时候自动触发垃圾回收机制.那么也需要WeakMap。
```javascript
	let map = new WeakMap();
	const key = document.getElementById('div');
	map.set(key, '这是一个div');

	map.get(key) // "这是一个div"

	//移除该元素
	key.parentNode.removeChild(key);
	key = null;
```