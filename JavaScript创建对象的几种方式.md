---
title: JavaScript创建对象的几种方式
categories: 技术
tags:
 - JavaScript
date: 2020-02-03 15:34:24
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

- 使用对象字面量

这是创建对象**最简单**的方法。

使用对象文字，可以在一条语句中定义和创建对象。

对象文字指的是花括号 {} 中的名称:键值对（比如 age:62）。

例子：

```javascript
let person = {
	name:"小黄", 
	age:22, 
	eyeColor:"black", 
	say:function(){
       alert(this.name+"今年"+this.age+"岁!");
    }
};

person.say()//调用
```

- 通过关键词 new

例子:
```javascript
let person = new Object();
person.name = "小黄";
person.age = 22;
person.eyeColor = "black"; 
person.say = function(){
	alert(this.name + "今年" + this.age + "岁!");
};

person.say()//调用
```

- 通过构造函数
	- 无参构造函数
	
	例子:
```	javascript
	function Person(){}
	let person = new Person(); 
	person.name = "小黄";
	person.age = 22;
	person.eyeColor = "black"; 
	person.say = function() {
			alert(person.name+"今年"+person.age+"岁!");
	};
	
	person.say()//调用
```
	
	- 带参构造函数
	
	例子:
```javascript
	function Person(name, age, eyeColor) { 
		this.name = name; 
		this.age = age; 
		this.eyeColor = eyeColor; 
		this.say = function() { 
		alert(this.name + "今年" + this.age + "岁!"); 
		};
	}
	
	let person = new Person("小黄", 22, "black"); //实例化、创建对象
	person.say()//调用
```


- 工厂模式

例子:
```javascript
function createPerson(name, age, eyeColor) { 
	let person = new Object();
	person.name = name;
	person.age = age;
	person.eyeColor = eyeColor;
	person.say = function() { 
		alert(this.name + "今年" + this.age + "岁!"); 
	};
	return person;
}

let my = createPerson("小黄", 22, "black");//实例化
my.say();//调用
```

- 原型模式

例子:
```javascript
function Person(name, age, eyeColor){}
Person.prototype.name = "小黄";
Person.prototype.age = 22;
Person.prototype.eyeColor = "black"; 
Person.prototype.say = function() { 
  alert(this.name + "今年" + this.age + "岁!"); 
}

let person = new Person();
person.say();
```

- 构造函数和原型组合模式

例子:
```javascript
function Person(name, age, eyeColor){
    this.name = name;
    this.age = age;
	this.eyeColor = eyeColor; 
};

Person.prototype.say = function(){
    return this.name + "今年" + this.age + "岁!"
};

let person = new Person("小黄", 22, "black");
console.log(person.say());
```