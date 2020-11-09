---
title: margin 合并
categories: 技术
tags:
 - CSS
date: 2020-10-26 18:42:31
---

>本博客 [hjy-xh](https://hjy-xh.github.io/)，转载请申明出处


## 什么是 margin 合并

[MDN 定义](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)

块的上外边距(margin-top)和下外边距(margin-bottom)有时合并(折叠)为单个边距,其大小为单个边距的最大值(或如果它们相等,则仅为其中一个),这种行为称为边距折叠.

举个 🌰

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <style>
      body {
        writing-mode: vertical-lr;
      }
      div {
        margin: 30px;
      }
    </style>
  </head>

  <body>
    <div>hello</div>
    <div>world</div>
  </body>
</html>
```

- **块级元素**,但不包括浮动和绝对定位元素(他们可以让元素块状化)
- 只发生在垂直方向(不考虑**writing-mode**的情况,严格来说应该是只发生在和当前文档流方向的相垂直方向上,默认的文档流是水平流)


## margin 合并三种场景

- 相邻兄弟元素 margin 合并
- 父级和第一个/最后一个子元素
- 空的块级元素的 margin 合并

#### 相邻兄弟元素 margin 合并

例子如上

#### 父级和第一个/最后一个子元素

```html
<div class="father">
  <div class="son" style="margin-top: 30px;">hello world</div>
</div>

<div class="father" style="margin-top: 30px;">
  <div class="son">hello world</div>
</div>

<div class="father" style="margin-top: 30px;">
  <div class="son" style="margin-top: 30px;">hello world</div>
</div>
```

##### 如何阻止这里margin合并的发生?
可以进行的操作(满足一个条件即可):
- 父元素设置块状格式化上下文元素
- 父级和第一个/最后一个子元素之间添加内联元素进行分隔
- 父元素设置`border`
- 父元素设置`padding`

#### 空的块级元素的 margin 合并

```html
<div class="father">
  <div class="son"></div>
</div>
```

##### 如何阻止这里margin合并的发生?
可以进行的操作(满足一个条件即可):
- 设置`height`或者`min-height`
- 添加内联元素进行分隔
- 父元素设置`border`
- 父元素设置`padding`


## margin 合并的计算规则
- 同向取极值
```html
<div style="margin-bottom: 50px;">a</div>
<div style="margin-top: 20px;">b</div>
```
此时两个`div`的间距为50px

```html
<div style="height: 100px; background-color: antiquewhite;"></div>
    <div style="margin-bottom: -50px;">a</div>
    <div style="margin-top: -20px;">b</div>
```
此时两个`div`的间距为-50px,视觉上非50px

- 反向取差值
```html
<div style="margin-bottom: 50px;">a</div>
<div style="margin-top: -20px;">b</div>
```
此时两个`div`的间距为30px,即 50px+(-20px)


## margin 合并的意义

#### 为什么会有margin
最初CSS的设计本意就是为了图文信息的展示,有了默认的`margin`,图文就不会挤在一起,垂直方向上就可以层次分明.
比如说`<p></p>`,其`margin`默认单位为em,为什么使用相对单位?
浏览器的字号大小可以自定义,当我们自定义为较大值时,margin跟随变化,能够保持合适的排版.

####  margin 合并的意义
对于兄弟元素的`margin`合并其作用和em类似,都是为了让图文信息打得排版更舒服自然.

对于父子元素的`margin`合并的意义在于:在页面中任何地方插入和嵌套`<div></div>`都不会影响原来的块状布局.
```html
<div style="margin-top: 20px;"></div>
```
现在在该元素外嵌套一层`<div></div>`标签,如果没有合并规则,该元素和兄弟节点的间距可能就会变大,也就影响了原来的布局.

对于自身的`margin`合并的意义在于可以避免空标签的影响
```html
<p>a</p>
<p></p>
<p></p>
<p></p>
<p></p>
<p>b</p>
```
因为规则的存在,它和底下的代码是一样的效果
```html
<p>a</p>
<p>b</p>
```