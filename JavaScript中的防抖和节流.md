---
title: JavaScript中的防抖和节流
categories: 技术
tags:
  - JavaScript
date: 2020-04-28 22:18:41
---

> 本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

## 概述

防抖和节流是优化前端性能的一种手段。
以下情况可以考虑使用防抖和节流：

- DOM 频繁重绘
- 频繁请求后端接口
- 浏览器的 resize、scroll（适合用节流）
- 鼠标的 mounsemove、mouseover（适合用节流）
- input 输入框的 keypress（适合用防抖）

## 防抖(debounce)

核心思想：当事件被触发，延迟 n 秒后执行回调函数，如果在 n 秒内再次被触发，则重新计时延迟时间。

代码实现：

```javascript
function debounce(fn, delay) {
  var timer;
  return function () {
    var that = this;
    var args = arguments;
    timer && clearTimeout(timer);
    timer = setTimeout(function () {
      fn.apply(that, args);
    }, delay);
  };
}
```

## 节流

核心思想：在规定的一个单位时间内只能触发一次函数，如果在这个单位时间内出发多次函数，只有一次生效。

代码实现:

```javascript
function throttle(fn, delay) {
  var timer;
  return function () {
    var that = this;
    var args = arguments;
    if (timer) {
      return;
    }
    timer = setTimeout(function () {
      fn.apply(that, args);
      // 在执行完fn后清空timer,throttle触发可以进入计时器
      timer = null;
    }, delay);
  };
}
```

## 节流与防抖的异同

相同：在一段时间内防止函数被频繁调用，减少资源浪费，提升性能。
不同：防抖是固定时间内只执行一次，节流是间隔固定时间后执行。
