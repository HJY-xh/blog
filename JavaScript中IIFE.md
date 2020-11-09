---
title: JavaScript中IIFE
categories: 技术
tags:
 - JavaScript
date: 2020-02-06 18:10:25
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

## 概念

IIFE(Immediately-Invoked Function Expression) 立即执行函数表达式,也就是说在声明函数的同时立即调用该函数.
先看看IIFE的语法:
```javascript
(function(){
    let color = 'red';
    console.log(color);
})()//red
```
常规函数的定义和调用:
```javascript
function cola(){
    let color = 'red';
    console.log(color);
}

cola();//red
```

## 为什么有IIFE

如果只是为了执行一个函数,从上面的例子可以看出好处有限.实际上IIFE的出现是为了弥补JS在在scope方面的缺陷：JS只有全局作用域(global scope)、函数作用域(function scope),从ES6开始才有块级作用域(block scope).
在JS中，只有function才能实现作用域隔离，因此如果要将一段代码中的变量、函数等的定义隔离出来，只能将这段代码封装到一个函数中。
```javascript
let color = 'blue';
function cola() {
    let color = 'red';
    return color;
}
console.log(cola());//red
console.log(color);
```

在我们通常的理解中，将代码封装到函数中的目的是为了复用。在JS中，声明函数的目的在大多数情况下也是为了复用，但是JS迫于作用域控制手段的贫乏，我们也经常看到只使用一次的函数：目的就是为了隔离作用域.
既然只使用一次，那么立即执行好了！既然只使用一次，函数的名字也省掉了！这就是IIFE的由来。

## IIFE一些要注意的地方
```javascript
(function () {
    //statements
})();
```

- 表达式中的变量不能从外部访问
```javascript
	(function () {
	let color = 'red';
	return color;
	})()//red

	console.log(color);//抛出错误 : ReferenceError: color is not defined
```


- 将IIFE分配给一个变量，不是存储 IIFE 本身，而是存储 IIFE 执行后返回的结果
```javascript
	let res = (function () {
		let color = 'red';
		return color;
	})();

	console.log(res);//red
```

- IIFE的多参数
```javascript
	(function (window, document) {  
		// 这里可以调用到window和document    
	})(window, document); 
```
## 总结
IIFE的目的是为了隔离作用域,防止污染全局命名空间.