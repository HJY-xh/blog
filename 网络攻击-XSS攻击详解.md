---
title: 网络攻击-XSS攻击详解
categories: 技术
tags:
 - 网络攻击
 - Web安全
date: 2020-03-10 13:39:41
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处

## XSS 概念
跨站脚本攻击(XSS),是最普遍的Web应用安全漏洞.

这类漏洞能够使得攻击者嵌入恶意脚本代码到正常用户会访问到的页面中,当正常用户访问该页面时,则可导致嵌入的恶意脚本代码的执行,从而达到恶意攻击用户的目的.

人们经常将跨站脚本攻击(Cross Site Scripting)缩写为 CSS ,但这会与层叠样式表(Cascading Style Sheet,CSS)的缩写混淆,因此将其改为 XSS .

## XSS 危害
- 盗用 cookie ,获取敏感信息

## XSS 原理
HTML 是一种超文本标记语言,通过特殊对待一些字符来区别文本和标记:小于符号(<)被看作是 HTML 标签的开始,浏览器会将特定的字符误认为HTML 标签.当 HTML标签引入了 JavaScript 脚本时,浏览器就会执行.因此,当这些特殊字符不能被动态页面检查或检查出错的时候,就会产生 XSS 漏洞

## XSS 类型
- 反射型(非持久型):指发生请求时,XSS代码出现在请求URL中,作为参数提交到服务器,服务器解析并响应,响应结果中包含XSS代码,最后被浏览器解析并执行.这个过程像一次反射,故叫做反射型XSS.
- 存储型(持久型):将XSS代码存储在服务器中.
- DOM跨站:指受害者端的网页脚本在修改本地页面DOM环境时未进行合理的处置,而使得攻击脚本被执行.在整个攻击过程中,服务器响应的页面并没有发生变化,引起客户端脚本执行结果差异的原因是对本地DOM的恶意篡改利用

## 具体示例
- 反射型XSS
假如一个接口`http://www.test.com/xss/reflect.php`的代码如下:
```php
<?php
echo 'x'
>
```
这里的x值没有经过处理直接输出,当客户端提交请求`http://www.test.com/xss/reflect.php?x=<script>alert(1)</script>`,此时浏览器会触发alert()函数.

- 存储型XSS
最典型的例子就是留言板XSS,当用户提交了一条包含XSS代码的留言存储到数据库,目标用户查看留言板时,留言内容会从数据库提取并展示在页面上,浏览器发现有XSS代码,就当成HTML和JavaScript解析执行,从而触发XSS攻击.简单的可以是一个alert()弹窗,复杂一些的可以是盗用用户cookie等操作.

- DOM XSS
DOM XSS攻击并不需要服务器参与,触发攻击靠浏览器的DOM解析,核心是运用DOM函数.
我们知道`eval()`函数有一个作用是将一段字符串转换成`JavaScript`语句,因此在`JavaScript`中使用`eval()`是一件有风险的事情,容易造成XSS攻击.
    ```javascript
    eval("alert('Hello world')")
    ```

    可能触发DOM XSS攻击的属性有:

    - document.referer属性
    - window.name属性
    - location属性
    - innerHTML属性
    - documen.write

## 防范手段
- 入参字符过滤
对诸如`<script>`、`<img>`、`<a>`等标签进行过滤.
- 出参进行编码
像一些常见的符号,如`<>`在输入的时候要对其进行转换编码,这样做浏览器是不会对该标签进行解释执行的,同时也不影响显示效果.
- 入参长度限制
xss攻击要能达成往往需要较长的字符串,因此对于一些可以预期的输入可以通过限制长度强制截断来进行防御.
