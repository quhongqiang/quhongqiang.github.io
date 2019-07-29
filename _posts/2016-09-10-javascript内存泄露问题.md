---
layout: post
header-img: "img/js2.jpg"
header-mask: 0.4
author: '曲小强'
title: 4类 JavaScript 内存泄露
subtitle: 探索常见的客户端JavaScript 内存泄露.
catalog: true
tags: 
  - Js
  - 内存泄露
---

### 简介
<a href="https://baike.baidu.com/item/%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F/6181425?fromtitle=%E5%86%85%E5%AD%98%E6%B3%84%E9%9C%B2&fromid=305116&fr=aladdin" target="_blank">内存泄露</a>是每个开发者最终都要面对的问题，它是许多问题的根源：`反应迟缓，崩溃，高延迟，以及其他应用问题`。

### 什么是内存泄露？ 
本质上，内存泄露可以定义为：应用程序不再需要占用内存的时候，由于某些原因，内存没有被操作系统或可用内存池回收。编程语言管理内存的方式各不相同。只有开发者最清楚哪些内存不需要了，操作系统可以回收。一些编程语言提供了语言特性，可以帮助开发者做此类事情。另一些则寄希望于开发者对内存是否需要清晰明了。

### JavaScript 内存管理
**JavaScript**是一种垃圾回收语言。垃圾回收语言通过周期性地检查先前分配的内存是否可达，帮助开发者管理内存。换言之，垃圾回收语言减轻了“`内存仍可用`”及“`内存仍可达`”的问题。两者的区别是微妙而重要的：仅有开发者了解哪些内存在将来仍会使用，而不可达内存通过算法确定和标记，适时被操作系统回收。

### JavaScript 内存泄露
垃圾回收语言的内存泄露主因是不需要的引用。理解它之前，还需了解垃圾回收语言如何辨别内存的可达与不可达。

### Mark-and-sweep
大部分垃圾回收语言用的算法称之为<a href="https://www.breakyizhan.com/java/4988.html" target="_blank">Mark-and-sweep</a>。算法由以下几步组成：
  - 垃圾回收器创建了一个“roots”列表。Roots 通常是代码中全局变量的引用。JavaScript 中，“window” 对象是一个全局变量，被当作 root 。window 对象总是存在，因此垃圾回收器可以检查它和它的所有子对象是否存在（即不是垃圾）；
  - 所有的 roots 被检查和标记为激活（即不是垃圾）。所有的子对象也被递归地检查。从 root 开始的所有对象如果是可达的，它就不被当作垃圾。
  - 所有未被标记的内存会被当做垃圾，收集器现在可以释放内存，归还给操作系统了。
现代的垃圾回收器改良了算法，但是本质是相同的：可达内存被标记，其余的被当作垃圾回收。

不需要的引用是指开发者明知内存引用不再需要，却由于某些原因，它仍被留在激活的 root 树中。在 JavaScript 中，不需要的引用是保留在代码中的变量，它不再需要，却指向一块本该被释放的内存。有些人认为这是开发者的错误。

为了理解 JavaScript 中最常见的内存泄露，我们需要了解哪种方式的引用容易被遗忘。

三种类型的常见 JavaScript 内存泄露

#### 1. 意外的全局变量
JavaScript 处理未定义变量的方式比较宽松：未定义的变量会在全局对象创建一个新变量。在浏览器中，全局对象是 **window** 。
```javascript
function foo(arg) {
  bar = "this is a hidden global variable";
}

// 真相是：
function foo(arg) {
  window.bar = "this is a hidden global variable";
}
```
函数 foo 内部忘记使用 var ，意外创建了一个全局变量。此例泄露了一个简单的字符串，无伤大雅，但是有更糟的情况。

另一种意外的全局变量可能由this创建:
```javascript 
function foo() {
  this.variable = "potential accidemtal global";
}
// Foo 调用自己，this指向了全局对象（window）
// 而不是underined
foo();
```
在 JavaScript 文件头部加上 `'use strict'`，可以避免此类错误发生。启用严格模式解析 JavaScript ，避免意外的全局变量。

**全局变量注意事项**

尽管我们讨论了一些意外的全局变量，但是仍有一些明确的全局变量产生的垃圾。它们被定义为不可回收（除非定义为空或重新分配）。尤其当全局变量用于临时存储和处理大量信息时，需要多加小心。如果必须使用全局变量存储大量数据时，确保用完以后把它设置为 null 或者重新定义。与全局变量相关的增加内存消耗的一个主因是缓存。缓存数据是为了重用，缓存必须有一个大小上限才有用。高内存消耗导致缓存突破上限，因为缓存内容无法被回收。

#### 2. 被遗忘的计时器或回调函数

在 JavaScript 中使用 **setInterval** 非常平常。一段常见的代码：

```javascript
var someResource = getData();
setInterval(function () {
  var node = document.getElementById('node');
  if (node) {
    // 处理 nide 和 someResource
    node.innerHTML = JSON.stringify(someResource);
  }
}, 1000);
```
此例说明了什么：与节点或数据关联的计时器不再需要，node 对象可以删除，整个回调函数也不需要了。可是，计时器回调函数仍然没被回收（计时器停止才会被回收）。同时，someResource 如果存储了大量的数据，也是无法被回收的。

对于观察者的例子，一旦它们不再需要（或者关联的对象变成不可达），明确地移除它们非常重要。老的 IE 6 是无法处理循环引用的。如今，即使没有明确移除它们，一旦观察者对象变成不可达，大部分浏览器是可以回收观察者处理函数的。

观察者代码示例：
```javascript
var element = document.getElementById('button');
function onClick(e) {
  element.innerHTML = 'text';
}

element.addEventListener('click', onClick);
```
**对象观察者和循环引用注意事项**

老版本的 IE 是无法检测 DOM 节点与 JavaScript 代码之间的循环引用，会导致内存泄露。如今，现代的浏览器（包括 IE 和 Microsoft Edge）使用了更先进的垃圾回收算法，已经可以正确检测和处理循环引用了。换言之，回收节点内存时，不必非要调用 removeEventListener 了。

#### 3：脱离 DOM 的引用
有时，保存 DOM 节点内部数据结构很有用。假如你想快速更新表格的几行内容，把每一行 DOM 存成字典（JSON 键值对）或者数组很有意义。此时，同样的DOM 元素存在两个引用：一个在 DOM 树中，另一个在字典中。将来你决定删除这些行时，需要把两个引用都清除。

```javascript
var elements = {
  button: document.getElementById('button'),
  image: document.getElementById('image'),
  text: document.getElementById('text')
};

function doStuff() {
  image.src = 'http://some.url/image';
  button.click();
  console.log(text.innerHTML);
}

function removeButton() {
  // 按钮是 body 的后代元素
  document.body.removeChild(document.getElementById('button'));
  // 此时，依旧存在一个全局的 #button 的引用
  // elements 字典。 button 元素仍旧在内存中，不被GC回收。
}
```
此外还要考虑 DOM 树内部或子节点的引用问题。假如你的 JavaScript 代码中保存了表格某一个 <td> 的引用。将来决定删除整个表格的时候，直觉认为 GC 会回收除了已保存的 <td> 以外的其它节点。

**实际情况并非如此**：此 <td> 是表格的子节点，子元素与父元素是引用关系。由于代码保留了 <td> 的引用，导致整个表格仍待在内存中。保存DOM 元素引用的时候，要小心谨慎。

#### 4.闭包
闭包是 JavaScript 开发的一个关键方面：匿名函数可以访问父级作用域的变量。

```javascript
var theThing = null;
var replaceThing = function () {
  var originalThing = theThing;
  var unused = function () {
    if (originalThing) {
      console.log('hi')
    }
  }；
  theThing = {
    longStr: new Array(1000000).join('*'),
    someMethod: function () {
      console.log(someMessage)
    }
  }
}

setInterval(replaveThing, 1000);
```
代码片段做了一件事情：每次调用 replaceThing ，theThing 得到一个包含一个大数组和一个新闭包（someMethod）的新对象。同时，变量 unused 是一个引用 originalThing 的闭包（先前的 replaceThing又调用了 theThing ）。思绪混乱了吗？最重要的事情是，闭包的作用域一旦创建，它们有同样的父级作用域，作用域是共享的。someMethod 可以通过 theThing 使用，someMethod 与 unused 分享闭包作用域，尽管unused 从未使用，它引用的 originalThing 迫使它保留在内存中（防止被回收）。当这段代码反复运行，就会看到内存占用不断上升，垃圾回收器（GC）并无法降低内存占用。本质上，闭包的链表已经创建，每一个闭包作用域携带一个指向大数组的间接的引用，造成严重的内存泄露。

Meteor 的博文 解释了如何修复此种问题。在 replaceThing 的最后添加 `originalThing= null` 。






























