---
title: Promise规范翻译
categories: 技术
tags:
 - JavaScript
date: 2020-11-09 21:30:40
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

[原文](https://promisesaplus.com/)

一个开放标准,通用的 JavaScript promise ,由开发者制定，供开发者使用。

Promise 用于表示一个异步操作的最终结果。与 Promise 交互的主要方式为`then`方法,该方法注册回调函数以接收 promise 的成功的结果或者失败的原因。

该规范详细说明了`then`方法的行为,所有基于 Promise/A+规范实现的 Promise 都必须以此为基础实现。因此规范应该非常稳定。尽管 Promises/A+组织可能会偶尔修改此规范以向后兼容,只有经过仔细考虑，讨论和测试后，我们才会集成大型或不向后兼容的更改。

从其发展过程来看，Promises/A+其实是把之前的`Promises/A提案`归纳总结为标准。扩展了一些现有的行为规范，并删除了一些有问题的、未明确的部分。

最终，Promises/A+规范核心内容不包括怎样处理 promises 的创建（create）,完成（fulfill）和拒绝（reject），而是选择专注于提供一个通用的，具备可互操作的`then`方法。上述的操作方法可能会在未来修改该规范时提及。

### 1. 术语

1.1 "promise" 是一个拥有`then`方法的对象或函数，其行为符合本规范
1.2 "thenable" 是一个定义了`then`方法的对象或函数
1.3 "value" 可以是任何 JavaScript 的合法值（包括 undefined, thenable 和 promise）
1.4 “exception” 是一个使用`throw`语句抛出的值。
1.5 “reason”是一个表示 promise 失败的原因

### 2. 要求

#### 2.1 Promise 要求

一个 Promise 必须处于以下三种状态中的其中一种: pending（等待）, fulfilled（完成）, 或 rejected（拒绝）。

2.1.1 当 promise 处于 pending 状态

- 2.1.1.1. 可以转换到 fulfilled 或 rejected 的状态。

  2.1.2 当 promise 处于 fulfilled 状态

- 2.1.2.1 不能再转换状态。
- 2.1.2.2 必须有一个值(value),且不可改变。

  2.1.3 当 promise 处于 rejected 状态

- 2.1.3.1 不能再转换状态。
- 2.1.3.2 必须有一个原因(reason),且不可改变。

这里的不可改变指的是恒等（即 `===` ），而不是意味着其内部的不可变（即仅仅是其引用地址不变，但属性值可被更改）。

#### 2.2 then 方法

一个 promise 必须提供一个`then`方法以读取其当前值、终值和失败原因。
一个 promise 的`then`方法接收两个参数:

```javascript
promise.then(onFullfilled, onRejected);
```

2.2.1 `onFulfilled`和`onRejected`都是可选的参数:

- 2.2.1.1 如果`onFullfilled`不是一个函数，则它会被忽略
- 2.2.1.2 如果`onRejected`不是一个函数，则它会被忽略

  2.2.2 如果`onFulfilled`是一个函数

- 2.2.2.1 必须在 `promise` 执行结束后执行，`promise`的 value 作为第一个参数
- 2.2.2.2 在 `promise` 执行结束前不可被调用
- 2.2.2.3 不能被多次调用

  2.2.3 如果`onRejected`是一个函数

- 2.2.3.1 必须在 `promise` 被拒绝后执行,`promise`的 reason 作为第一个参数
- 2.2.3.2 在 `promise` 执行结束前不可被调用
- 2.2.3.3 不能被多次调用

  2.2.4 `onFulfilled`和`onRejected`只有在执行环境堆栈仅包含平台代码时才可被调用 [3.1](####3.1)

  2.2.5 `onFulfilled`和`onRejected`必须被作为函数调用（即没有 this 值） [3.2](####3.2)

  2.2.6 `then`方法可以被同一个 promise 调用多次

- 2.2.6.1 如果/当 `promise`是 fulfilled 状态，则所有相应的`onFulfilled`回调必须按注册顺序执行`then`方法
- 2.2.6.2 如果/当 `promise`是 rejected 状态，则所有相应的`onRejected`回调必须按注册顺序执行`then`方法

  2.2.7 `then`方法必须返回一个 promise 对象[3.3](####3.3)

```javascript
promise2 = promise1.then(onFulfilled, onRejected);
```

- 2.2.7.1 如果`onFulfilled`或者`onRejected`返回一个值`x` ，则运行下面的 Promise 解决过程：`[[Resolve]](promise2, x)`
- 2.2.7.2 如果`onFulfilled`或者`onRejected`抛出一个异常`e` ，则`promise2`必须拒绝执行，并将`e`作为拒绝原因
- 2.2.7.3 如果`onFulfilled`不是一个函数并且`promise1`已经完成，则`promise2`必须使用与`promise1`相同的 value 来 fulfilled
- 2.2.7.4 如果`onRejected`不是一个函数并且`promise1`已经完成，则`promise2`必须使用与`promise1`相同的 reason 来 rejected

#### 2.3 Promise 解决过程

Promise 解决过程是一个抽象的操作,其需输入一个 promise 和一个值,我们表示为`[[Resolve]](promise, x)`,如果 x 是一个 thenable(promise),它会尝试采用 x 的状态,前提是`x`行为至少有点像 promise。否则，它作为`promise`的 fulfilled 的 value 返回。
对 thenables 的这种处理允许 promise 实现进行更具有通用性：只要其暴露出一个遵循 Promise/A+ 协议的 then 方法即可；这同时也使遵循 Promise/A+ 规范的实现可以与不冲突的实现能良好共存。

要运行`[[Resolve]](promise, x)`, 需遵循以下步骤：

- 2.3.1 如果`promise`和`x`指向同一对象，以`TypeError`为拒绝原因拒绝执行`promise`
- 2.3.2 如果`x`为 Promise,则使 promise 接受 x 的状态[3.4](####3.4)
  - 2.3.2.1 如果`x`处于 pending,promise 需保持 pending,直至`x`被执行或拒绝
  - 2.3.2.2 如果`x`执行完毕,用相同的值执行`promise`
  - 2.3.2.3 如果`x`被拒绝,用相同的原因拒绝`promise`
- 2.3.3 如果`x`为对象或者函数
  - 2.3.3.1 把`x.then`赋值给`then`[3.5](####3.5)
  - 2.3.3.2 如果取`x.then`的值时抛出错误`e`,则用`e`作为 promise 的拒绝原因
  - 2.3.3.3 如果`then`是函数,将`x`作为函数的作用域`this`调用之。传递两个回调函数作为参数,第一个参数叫做`resolvePromise`,第二个参数叫做`rejectPromise`:
    - 2.3.3.3.1 如果`resolvePromise`以值`y`为参数被调用，则运行 `[[Resolve]](promise, y)`
    - 2.3.3.3.2 如果`rejectPromise`以拒绝原因`r`为参数被调用,则以`r`拒绝`promise`
    - 2.3.3.3.3 如果`resolvePromise`和`rejectPromise`均被调用,或者被同一参数调用了多次,则优先采用首次调用并忽略其余调用
    - 2.3.3.3.4 如果调用`then`方法时抛出异常`e`,
      - 2.3.3.3.4.1 如果`resolvePromise`或`rejectPromise`已经被调用,就忽略它
      - 2.3.3.3.4.2 否则以`e`为原因拒绝`promise`
  - 2.3.3.4 如果`then`不是函数,以`x`为参数执行`promise`
- 2.3.4 如果`x`不为对象或者函数,以`x`为参数执行`promise`

如果一个 promise 被一个循环的 thenable 链中的对象解决，而`[[Resolve]](promise, thenable)`的递归性质导致其被再次调用,根据上述的算法将会陷入无限递归。规范鼓励施者检测这样的递归是否存在,但不强制,如果检测到存在则以一个可识别的`TypeError`为原因来拒绝 `promise`。[3.6](####3.6)

### 3. 备注

#### 3.1

这里的“平台代码”指的是引擎,环境和 promise 实现代码。实践中要确保`onFulfilled`和`onRejected`方法异步执行,且应该在`then`方法被调用的那一轮事件循环之后的新执行栈中执行。这可以用“宏任务”机制实现,例如`setTimeout`或者`setImmediate`,或者用“微任务”机制,例如`MutationObserver`或`process.nextTick`。由于 promise 实现被认为是平台代码,因此它本身可能包含一个任务调度队列或“trampoline”的处理程序。

#### 3.2

也就是说在严格模式（strict）中，`this`的值为`undefined`;在非严格模式中其为全局对象。

#### 3.3

代码实现在满足所有要求的情况下可以允许`promise2 === promise1`。每个实现都要文档说明其是否允许以及在何种条件下允许 p`romise2 === promise1`。

#### 3.4

一般来说,`x`如果它来自当前的实现,那么它是一个真正的 promise。该子句允许使用特定于实现的方法来采用已知符合的 promise 的状态。

#### 3.5

此过程首先存储引用`x.then`,然后测试,调用该引用,避免多次访问该`x.then`属性。这么做的原因是防止每次获取该值时,返回不同的情况（ES5 的 getter 特性可能会产生副作用）

#### 3.6

实现不应该对 thenable 链的深度设限,并假定超出本限制的递归就是无限循环。只有真正的循环递归才应能导致`TypeError`异常；如果一条无限长的链上 thenable 均不相同,那么递归下去永远是正确的行为。
