---
layout: post
header-img: "img/js2.jpg"
header-mask: 0.4
author: '曲小强'
title: 怎样书写可维护JavaScript
subtitle: 为了不逼疯开发者，如何书写可维护的JavaScript代码。
catalog: true
tags: 
  - Js
---

几乎每个开发者都有接手过维护遗留项目的经历，或者说是一个旧的项目想继续维护起来。通常第一反应是抛开它们代码规范基础，按自己的意思去写。这样代码会很乱，不可理解，并且别人可能要花费好几天去读懂代码。但是，如果结合正确的规划、分析、和一个好的工作流，那就有可能把一个面条式的代码仓库整理成一个整洁、有组织并易扩展的一份项目代码。

### 一、分析项目
如果是个网站，点击网站所有的功能：`打开对话框`、`发送表单`等等。做这些的同时，打开开发者工具，看下是否报错或输出日志。如果是个**nodeJs**项目，打开命令行接口过一下`api`，最好的情况是项目有一个入口(例如:`main.js，index.js，app.js`)，通过入口能将所有的模块初始化；或者最坏的情况下，也要找到每个业务逻辑的位置。

找出使用的工具。`jquery`?`React`?`Express`?列出需要了解一些一切重要的东西。如果所在项目使用`angular2`写的，而你还没有使用过，直接去看文档先有个基本的了解。总之需要找下最好的开始方案。

> __在更高的层次上看项目__

要知道技术是一个好的开始，但是为了有一个真实的感觉和理解，是时候研究下单元测试了。单元测试是用来测试功能和代码的方法是否按预期调用的一种方式。相比阅读代码和运行代码，单元测试能更深入的帮你了解代码。如果在你的项目中还没有单元测试，别急，我们接着往下看。

### 二、创建一个基准
这些都是关于代码一致性的内容。现在你已经了解了项目中使用的所有工具集，你知道了代码的结构和逻辑功能的位置，是时候建立一个基准了。我建议添加一个`.editorconfig`文件来保证代码在不同的编辑器、ide或不同的开发者之间的编写风格一致。这我是项目中曾用到过的<a href="https://github.com/quhongqiang/note/blob/master/.editorconfig" target="_blank">.editorconfig</a>

> **正确的缩进**

这是一个饱受争议的问题(跟战争一样)，代码中使用`空格(space)`还是`tab`，其实这不重要。如果之前代码用的空格，那么使用空格，如果使用tab，继续使用。如果代码中都使用到了，那么是时候决定使用哪个了。讨论的观点是好的，但是一个好的项目目的是必须保证所有的开发者能在一起和谐的工作。

> **命名**

保证项目里面使用到的命名规则是合理的。通常**JavaScript**里面一般使用`驼峰式命名方式`，但是我看到了很多混合式的命名方式。举例来说，jquery项目常常含有jquery变量和其他变量的混合命名。

```javascript
const $element = $('.elememt');
function _privateMethod() {
  const self = $(this);
  let $data = element.data('foo');
  // 等一系列的命名
}
// 这是更容易和更快的理解
function _privateMethod() {
  const $this = $(this);
  let elementData = $element.data('foo');
}
```
> **尽可能使用 lint**

前面一点就会使我们的代码变得好看些，能够帮助我们快速地浏览代码，这里我们通常还要推荐使用保证代码整洁性的最佳实践方案。`ESlint JSlint`,`JSHint`是现在最流行的JavaScript格式工具。个人来说，之前使用JSLint比较多，现在开始觉得ESlint很不错，主要是它的一些自定义规则和最早支持ES2015语法很好用。

如果你使用lint时，编辑器报了一堆错误，那么修复它们，在此之前什么也不要做。

### 三、搞点事情
这里我主要要表达的是整理项目并不一定意味着移除或重写大部分的代码。当然，有时候这时唯一的解决方案，但是这不是你一开始就应该考虑的问题。`JavaScript`代码很可能成为一个奇怪的代码，后面去做一些调整通常是不可能的。我们通常需要根据特定的场景来给出一个改造方案。

> **建立测试用例**

使用测试用例可以保证你的代码能进行正确的运行而不会出现意外的错误。JavaScript单元测试直接可以写出很多文章，所有我这里没办法介绍太多。广泛使用的框架有`karma、jasmine、macha和ava`等。

单元测试和浏览器自动化测试的区别是，前者测试`JavaScript`本身代码。它保证了所有的模块和通用逻辑能预期运行。浏览器自动化，另一方面来说是测试界面，也就是项目的用户界面，保证页面上的元素在预期正确的位置。

> **架构**

JavaScript架构是另一个大的主题。重构和整理框架决定于你在这方面有多少经验。我们在软件开发中有很多的设计模式。但是并不是所有的都能适应稳定性的需求。不幸的是，这篇文章中我不能给出所有的场景，但至少还是可以给一些通用性的建议:

首先，你要知道你的项目中使用到了那种**设计模式**。了解下这种模式，并保证它在整个项目是一致的。稳定性一个关键的地方是和设计模式紧密结合的，而不是混合的方法技术。当然，在你的项目里可以使用不同的设计模式来达到不同的目的(例如使用单例来建立数据结构或者短命名的工具函数，或者在模块中使用观察者模式)，但是绝对不要一个模块使用一种设计模式，另一个模块使用另一个模式。

如果在你的项目中个确实没有使用到什么架构(可能什么东西都是一个巨大的huge.js)，那么是时候改变它了。但不要马上做所有的改变，而是一点一点的来。同样，这里没有通用的方法，每个项目的设置也是不一样的。项目目录结构根据项目的规模和复杂度不同也不一样。通常，对于最基本的层级，结构一般分为分为第三方内容、模块内容、数据和一个初始化所有模块和逻辑的入口(例如index.js、main.js)。这样我们就需要模块化了。

> **所有的东西都模块化？**

模块化至今也不是大规模可扩展JavaScript项目的解决方案。它需要开发者必须去熟悉另一层api。尽管这样可能会带来很多的困难，但它的原则是把你的功能划分成小的模块。这样，在团队协作过程中解决问题就变的更简单了。每个模块应该有个一个明确的目标功能点。一个模块应该是不知道你外面代码逻辑是什么样的，并且能在不同的地方和场景下复用。

那么怎样将大量关联逻辑的代码拆分成模块呢？一起看下:
```javascript
fetch(url, {
    method: 'GET'
  })
  .then(response => {
    if (response.status === 200) {
      return respnose.json();
    }
  })
  .then(json => {
    if (json) {
      Object.keys(json).forEach(key => {
        const item = json[key];
        const count = item.content.trim().replace(/\s+/gi, '').length;
        const el = `<div class="foo-${item.className}">
                      <p>Total characters: ${count}</p>
                    </div>`;
        const wrapper = document.querySelector('.info-element');
        wrapper.innerHTML = el;
      })
    }
  })
  .catch(error => console.error(error));
```
这里基本没有模块化。所有的东西都是紧密结合的，并且相互依赖。想象一下在更大、更复杂的函数里，如果出了问题你要来`debug`。可能api不响应、json里面字段改变了或者其他的。这简直疯掉。
```javascript
function countCharacters(text) {
  const removeWhitespace = '/\s+/gi';
  return text.trim().replace(removeWhitespace, '').length;
}

function createWrapperElement(cssClass, content) {
  const className = cssClass || 'default';
  const wrapperElement = document.createElement('div');
  const textElement = document.createElement('p');
  const textNode = document.createTextNode(`Total characters:${content}`);
  
  wrapperElement.classList.add(className);
  textElement.appendChild(textNode);
  wrapperElement.appendChild(textElement);

  return wrapperElement;
}

function appendCharacterCount(config) {
  const wordCount = countCharacters(config.content);
  const wrapperElement = createWrapperElement(config.className, wordCount);
  const infoElement = document.querySelector('.info-element');

  infoElement.appendChild(wrapperElement);
}
```
很好，我们现在有三个模块了，我们来看下调用的情况：

```javascript
fetch(url, {
  method: 'GET'
})
.then(response => {
  if (response.status === 200) {
    return response.json();
  }
})
.then(json => {
  if (json) {
    Object.keys(json).forEach(key => {
      appendCharacterCount(json[key])
    })
  }
})
.catch(error => console.error(error));
```
我们将 `.then()` 里面的方法提取了出来，这里我想我已经向大家演示了模块化的意思了。

> **给代码添加文档注释**

文档是一个很重的讨论话题。一部分编程社区主张为任何东西书写文档，而另一部分人认为自带必要注释的代码就够了。就像生活中很多事情一样，我想两者之间一个好的平衡是最好的实践。这里推荐使用 JSDoc来管理你的文档。

JSDoc是一个JavaScript的api文档生成器。通常可以在ide插件里面使用。例如

```javascript
function properties(name, obj = {}) {
  if (!name) return ;
  let arr = [];
  Object.keys(obj).forEach(key => {
    if (arr.indexOf(obj[key][name]) <= -1) {
      arr.push(obj[key][name]);
    }
  });
  return arr;
}
```
这个函数接受两个参数后迭代一个对象，然后返回一个数组。这段代码不是很复杂，但是对于没有接触过这段代码的人还是需要一点时间来弄明白发生了什么事情。另外，函数做了什么事情不是很明确，所以文档可以这样写。

```javascript
/**
* @description 循环访问一个对象，将所有与“name”匹配
*               的属性推入一个新数组，但每次只出现一次。
* @param  {String} propertyName - 所需属性的名称
* @param  {Object} obj          -  要循环访问的对象
* @return {Array}
*/
function getArrayOfProperties(propertyName, obj = {}) {
  if (!propertyName) return;
  let properties = [];
  Object.keys(obj).forEach(child => {
    if (properties.indexOf(obj[child][propertyName]) <= -1) {
      properties.push(obj[child][propertyName]);
    }
  });
  return properties;
}
```
我没有接触太多代码本身。只是通过重命名了函数并且添加了一个简短的描述性注释块，这样就提升了代码的可读性。

### 四、总结一下

#### 1、分析项目
  - 不考虑你是开发者，把自己当成一个用户看下你的项目是什么东西
  - 浏览下代码看下用了哪些工具
  - 看些文档找下好的工具实践
  - 过下单元测试，从更高的角度上看下项目

#### 2、建立基准

  - 使用 `.editorconfig` 来保证不同编辑器之间的代码规范
  - 确定好缩进方式。tab或空格都无所谓
  - 保证命名规范
  - 如果还没使用，那么推荐使用格式工具，例如ESlint,JSlint，或 JSHint 等
  - 更新依赖，但是要理性的去慢慢升级

#### 3、整理
  - 使用 `Karma、Jasmine`或`Nightwatch.js`来建立单元测试或浏览器自动化测试
  - 保证架构和设计模式一致性
  - 不要混用设计模式，尽可能结合已有的设计模式
  - 决定你是否想将项目分离成模块。每个模块只做明确的一个功能的并且和外面的代码解耦
  - 如果不想做模块化，专注于分离成多个可测试的小型代码块
  - 为你的代码建立文档和合适的命名方式
  - 使用JSDoc来自动生成注释
  - 提交每一个代码变更。如果出错方便回滚

#### 4、不要疯掉
  - 不要抱怨前面的程序员。负面情绪不能帮你重构，只会浪费你的时间
  - 每行代码都有它存在的原因。记住我们写的代码也是


> [原文地址](https://www.sitepoint.com/write-maintainable-javascript/)