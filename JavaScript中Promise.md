---
title: JavaScript中Promise
categories: 技术
tags:
 - JavaScript
 - ES6
date: 2020-02-11 17:16:41
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

## Promise 出现的原因
在 Promise 出现以前,处理一个Ajax请求,大概是这样的:
```javascript
$.get('url', {data: data}, function(result){
    console.log('成功', result)// 成功的回调，result为异步拿到的数据
});
```

看起来还不错,但需求变化了,现在需要根据第前面的结果继续请求,代码大概如下：
```javascript
$.get('url', {data: data}, function(result1){
    $.get('url', {data: result1}, function(result2){
        $.get('url', {data: result2}, function(result3){
            $.get('url', {data: result3}, function(result4){
                ......
                $.get('url', {data: resultn}, function(resultn+1){
                    console.log('成功')
                }
            }
        }
    }
});
```

以上就是**回调地狱**,更糟糕的是,我们可能还要对每次请求的结果进行一些处理,代码会更加臃肿,后期维护也非常痛苦!

那么总结一下回调地狱的特点:
- 代码臃肿
- 可读性差
- 耦合度过高,可维护性差
- 代码复用性差
- 容易出现Bug
- 只能在回调里处理异常

后来出现了Promise,它以一种更加友好的代码组织方式,解决了异步嵌套的问题:
```javascript
let 请求结果1 = 请求1();
let 请求结果2 = 请求2(请求结果1); 
let 请求结果3 = 请求3(请求结果2); 
let 请求结果4 = 请求2(请求结果3); 
let 请求结果5 = 请求3(请求结果4); 
```
类似与上面同步的写法.于是Promise规范诞生了!

## 什么是Promise
Promise 对象用于表示一个异步操作的最终完成 (或失败), 及其结果值.

优点:
- 异步操作将以同步的流程表达出来,避免了层层嵌套的回调函数.
- Promise 对象提供了统一的接口,使得控制异步操作更容易.

缺点:
- 无法取消,一旦新建就会立即执行,无法中途取消.
- 若不设置回调函数,promise 内部会抛出错误,不会反映到外部

## Promise/A+ 规范
** Promise 规范 **有很多,如 Promise/A , Promise/B , Promise/D 以及 Promise/A 的升级版 Promise/A+ ,最终ES6采用了 **Promise/A+** 规范.

Promise 规范:
- 英文版: [https://promisesaplus.com/](https://promisesaplus.com/)
- 中文版: [http://malcolmyu.github.io/malnote/2015/06/12/Promises-A-Plus/](http://malcolmyu.github.io/malnote/2015/06/12/Promises-A-Plus/)

总结如下:
- 一个 Promise 对象有种个状态,并且状态一旦改变,便不能再被更改为其他状态.
	- **pending**: 异步任务正在执行.
	- **fulfilled**: 异步任务执行成功.
	- **rejected**: 异步任务执行失败.

- **then**方法可以被同一个 Promise 调用多次,且必须返回一个 Promise

## Promise 语法
```javascript
new Promise( function(resolve, reject) {/* executor */}   );
```
- Promise构造函数接受一个函数作为参数,该函数的两个参数分别是resolve和reject.它们是两个函数,由 JavaScript 引擎提供,不用自己部署.
- resolve 函数的作用:将Promise实例的状态从"pending"到"fulfilled",在异步操作成功时调用,并将异步操作的结果,作为参数传递出去.
- reject 函数的作用:将Promise实例的状态从"pending"到"rejected",在异步操作失败时调用,并将异步操作报出的错误，作为参数传递出去.

例子:
```javascript
let promise = new Promise(function (resolve, reject) {
    setTimeout(function () {
        resolve('Cola');
        console.log(promise);//Promise { 'Cola' }
    }, 300);
});

promise.then(function (value) {
    console.log(value);//Cola
});

console.log(promise);//Promise { <pending> }
```

输出顺序:
```javascript
Promise { <pending> }
Promise { 'Cola' }
Cola
```

## Promise 常用API
- Promise.resolve()
- Promise.reject()
- Promise.then(成功回调函数，失败回调函数)
- Promise.then(成功回调函数).catch(失败回调函数)
- Promise.then(成功回调函数).catch(失败回调函数).finally(成功失败都执行的函数)
```javascript
let promise = new Promise(function (resolve, reject) {
    setTimeout(function () {
        resolve('Cola');
    }, 300);
}).then(data => {
    console.log('I like ' + data)
}).catch(function (error) {
    console.log(error);
}).finally(function () {
    console.log('这是finally')
});
```
- Promise.all(iterable):方法返回一个 Promise 实例,此实例在 iterable 参数内所有的 promise 都"完成（resolved）"或参数中不包含 promise 时回调完成（resolve）;如果参数中 promise 有一个失败（rejected）,此实例回调失败（reject）,失败原因的是第一个失败 promise 的结果.
```javascript
let p1 = Promise.resolve('I');
let p2 = 'like'
let p3 = new Promise(function (resolve, reject) {
    setTimeout(function () {
        resolve('Cola');
    }, 300);
})
Promise.all([p1, p2, p3]).then(function (value) {
    console.log(value);//[ 'I', 'like', 'Cola' ]
});
```
- Promise.race(iterable):返回一个 promise,一旦迭代器中的某个promise解决或拒绝,返回的 promise就会解决或拒绝.
```javascript
const promise1 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 500, 'one');
});

const promise2 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 100, 'two');
});

Promise.race([promise1, promise2]).then(function(value) {
  console.log(value);//two
});
```



