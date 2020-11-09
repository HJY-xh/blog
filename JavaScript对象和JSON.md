---
title: JavaScript对象和JSON
categories: 技术
tags:
 - JavaScript
date: 2020-02-05 11:45:23
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

# 二者概念
JSON(JavaScript Object Notation)是一种轻量级的数据交换格式,它主要用于跨平台交流数据.
这种数据格式是从JavaScript对象中演变出来的，它是JavaScript的一个子集。JSON本身的意思就是JavaScript对象表示法（JavaScript Object Notation），它用严格的JavaScript对象表示法来表示结构化的数据，因此 JSON 的属性名必须有双引号。

JSON 数据结构有两种，这两种结构就是对象和数组，通过这两种结构可以表示各种复杂的结构。
```javascript
{
    "college": "TKK",
    "age": 16,
    "students":[
        {"firstName":"ITT","lastName":"16001"},
        {"firstName":"ITT","lastName":"16002"},
        {"firstName":"ITT","lastName":"16003"}
    ]
}
```

# 二者转换
- **JSON 数据转换为 JS 对象:eval()函数:**
```javascript
let txt = '{"college": "TKK","age": 16,"students": [{ "firstName": "ITT", "lastName": "16001" },{ "firstName": "ITT", "lastName": "16002" },{ "firstName": "ITT", "lastName": "16003" }]}'
let obj = eval("(" + txt + ")");
console.log(obj);
console.log(typeof obj);//object
```
使用eval()函数时,必须为传入的JSON数据添加括号"()",否则会报语法错误.该函数除了可以解析JSON数据,也可以用于执行JavaScript 脚本片段，这就会带来潜在的安全问题。

- **JSON 数据转换为 JS 对象:parse()函数:**
```javascript
let txt = '{"college": "TKK","age": 16,"students": [{ "firstName": "ITT", "lastName": "16001" },{ "firstName": "ITT", "lastName": "16002" },{ "firstName": "ITT", "lastName": "16003" }]}'
let obj = JSON.parse(txt);
console.log(obj);
console.log(typeof obj);//object
```
这是专门的 JSON Parser 来实现只用于解析 JSON 数据，不会执行 JavaScript 脚本，而且速度更快。

- **JS 对象转换为 JSON 数据:JSON.strigify() 函数**
```javascript
let obj = { college: "TKK", age: 16 };
let txt = JSON.stringify(obj);
console.log(txt);//"{"college":"TKK","age":16}"
```

#一张表格来区别二者:

区别 | Json | Javascript对象
------------- | ------------- | -------------
含义|	仅仅是一种数据格式|	对象的实例
传输|	可以跨平台数据传输，速度快|	不能传输
表现|	1. 键值对 2. 键必须加双引号 3. 值不能为方法函数/undefined/NaN|	1.键值对 2.值可以是函数、对象、字符串、数字、boolean 等
相互转换|	Json → JS 对象：1. var obj = JSON.parse(jsonstring); 2. var obj = eval("("+jsonstring+")");|	JS 对象 → Json：JSON.stringify(obj);