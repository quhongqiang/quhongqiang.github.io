---
layout: post
header-img: "img/post-data-bg.png"
header-mask: 0.4
author: '曲小强'
title: Ajax,jQuery ajax,axios和fetch介绍、区别以及优缺点
subtitle: 漫长的历史发展历程，前端与后端的交互从一开始的ajax到后来的多种数据请求技术，Jquery的Ajax、axios、fetch等他们是什么，怎么用？
catalog: true
tags: 
  - 随笔
---
### 引言

> 前端的发展可以说是一个快速崛起的历程了，不断的进化，不断的出现新的Api，新的功能，前端这个领域真的是一个发展飞快的领域，你前一天刚学会XXX的的运用，后一天某某某就革新了一项新的技术，你在感叹学不动的同时，不得不继续学习。扯远了，回到我们今天的主题。。。

三年前入职的时候还是一个只会使用<a href="https://developer.mozilla.org/zh-CN/docs/Web/Guide/AJAX" target="_blank">Ajax</a>和<a href="https://jquery.com/" target="_blank">Jquery Ajax</a>的菜鸟，由于早期Jquery不支持大文件请求的问题，可以原生的XHR解决。

_`以下篇幅较长，建议收藏了慢慢收看。在这里学到的肯定会对你有所帮助`_

### 介绍

<a href="https://developer.mozilla.org/zh-CN/docs/Web/Guide/AJAX" target="_blank">**「Ajax」:**</a>

> 全称`Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）`最早出现的发送后端请求技术，隶属于原始js中，核心使用`XMLHttpRequest`对象，多个请求之间如果有先后关系的话，就会出现回调地狱。

<a href="https://jquery.com/" target="_blank">**「Jquery Ajax」:**</a>

> 是 jQuery 底层 AJAX 实现。简单易用的高层实现见 `$.get`, `$.post` 等。`$.ajax()` 返回其创建的 `XMLHttpRequest` 对象。大多数情况下你无需直接操作该函数，除非你需要操作不常用的选项，以获得更多的灵活性。<a href="https://www.w3school.com.cn/jquery/ajax_ajax.asp" target="_blank">jQuery ajax - ajax() 方法</a>

<a href="http://www.axios-js.com/" target="_blank">**「Axios」:**</a>

>`axios`不是原生JS的，需要进行安装，它不但可以在客户端使用，也可以在nodejs端使用。Axios也可以在请求和响应阶段进行拦截。同样也是基于[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)对象的。特性：从浏览器中创建 XMLHttpRequests、从 node.js 创建 http 请求、支持 Promise API、拦截请求和响应等。[Axios 中文文档传送门](http://www.axios-js.com/zh-cn/docs/)

<a href="https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API" target="_blank">**「Fetch」:**</a>

> `Fetch` 提供了对 `Request` 和 `Response` （以及其他与网络请求有关的）对象的通用定义。使之今后可以被使用到更多地应用场景中：无论是`service workers`、`Cache API`、又或者是其他`处理请求和响应的方式`，甚至是任何一种需要你自己在程序中生成响应的方式。`Fetch`号称是AJAX的替代品，是在ES6出现的，使用了ES6中的[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)对象。Fetch是基于`promise`设计的。`Fetch`的代码结构比起`ajax`简单多了，参数有点像jQuery ajax。但是，一定记住fetch不是ajax的进一步封装，而是原生js。`Fetch`函数就是原生js，没有使用`XMLHttpRequest`对象。

### 细谈
详细的描述一下Ajax,jQuery ajax,axios和fetch区别，让我们继续往下研究。

> AJAX = 异步 JavaScript 和 XML。

> AJAX 是一种用于创建快速动态网页的技术。

通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

**AJAX 工作原理**

![Alt](https://github.com/quhongqiang/quhongqiang.github.io/blob/master/img/q1.png?raw=true)

#### 一、Ajax

[XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest) 让发送一个HTTP请求变得非常容易。你只需要简单的创建一个请求对象实例，打开一个URL，然后发送这个请求。当传输完毕后，结果的[HTTP状态](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/HTTP_response_codes)以及返回的响应内容也可以从请求对象中获取。简单的来叙述一下这个过程，往下看：

```javascript
function reqListener () {
  console.log(this.responseText);
}

var oReq = new XMLHttpRequest();
oReq.onload = reqListener;
oReq.open("get", "newFile.txt", true);
oReq.send();
```
##### 1-1 请求类型

通过`XMLHttpRequest`生成的请求可以有两种方式来获取数据，`异步模式`或`同步模式`。请求的类型是由这个`XMLHttpRequest`对象的`open()`方法的第三个参数`async`的值决定的。如果该参数的值为`false`，则该`XMLHttpRequest`请求以同步模式进行，否则该过程将以异步模式完成。

> `注意`：由于对用户体验的糟糕效果，从`Gecko 30.0(Firefox 30.0 / Thunderbird 30.0 / SeaMonkey 2.27)`版本开始，在主线程上的同步请求已经被`弃用`。

##### 1-2 处理响应

`W3C`规范定义了`XMLHttpRequest`对象的几种类型的[响应属性](https://xhr.spec.whatwg.org/)。这些属性告诉客户端关于`XMLHttpRequest`返回状态的重要信息。一些处理非文本返回类型的用例可能包含一些下面章节描述的操作和分析。

_1-2-1、分析并操作 responseXML属性_

如果你使用 `XMLHttpRequest` 来获得一个远程的 XML 文档的内容，`responseXML` 属性将会是一个由 `XML` 文档解析而来的 `DOM` 对象，这很难被操作和分析。这里有`五种主要的分析 XML 文档的方式`：
>- 1.使用 [XPath](https://developer.mozilla.org/zh-CN/docs/Web/XPath) 定位到文档的指定部分。
>- 2.使用 [JXON](https://developer.mozilla.org/zh-CN/docs/Archive/JXON) 将其转换成 JavaScript 对象树。
>- 3.手工的 [解析和序列化](https://developer.mozilla.org/zh-CN/docs/Web/Guide/Parsing_and_serializing_XML) XML 为字符串或对象。
>- 4.使用 XMLSerializer 把 DOM 树序列化成字符串或文件。
>- 5.如果你预先知道 XML 文档的内容，你可以使用 RegExp。如果你用 [RegExp](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp) 扫描时受到换行符的影响，你也许想要删除所有的换行符。然而，这种方法是"最后手段"，因为如果 XML 代码发生轻微变化，该方法将可能失败。

_1-2-2、解析和操作包含 HTML 文档的 responseText 属性_

如果使用 `XMLHttpRequest` 从远端获取一个 `HTML` 页面，则所有 HTML 标记会以字符串的形式存放在`responseText` 属性里，这样就使得操作和解析这些标记变得困难。解析这些HTML标记主要有`三种方式`：

>- 1.使用 `XMLHttpRequest.responseXML` 属性。
>- 2.将内容通过 `fragment.body.innerHTML` 注入到一个 文档片段 中，并遍历 DOM 中的片段。
>- 3.如果你预先知道 HTML 文档的内容，你可以使用 `RegExp` 。如果你用 RegExp 扫描时受到换行符的影响，你也许想要删除所有的换行符。 然而，这种方法是"最后手段"，因为如果 HTML 代码发生轻微变化，该方法将可能失败。

##### 1-3 处理二进制数据

尽管 `XMLHttpRequest` 一般用来发送和接收文本数据，但其实也可以发送和接受二进制内容。有许多经过良好测试的方法来强制使用 XMLHttpRequest 发送二进制数据。利用 `XMLHttpRequest.overrideMimeType()` 方法是一个解决方案，虽然它并不是一个标准方法。
```javascript
var oReq = new XMLHttpRequest();
oReq.open("GET", url, true);
// retrieve data unprocessed as a binary string
oReq.overrideMimeType("text/plain; charset=x-user-defined");
```

在 XMLHttpRequest Level 2 规范中新加入了 `responseType` 属性 ，使得发送和接收二进制数据变得更加容易。
```javascript
var oReq = new XMLHttpRequest();

oReq.onload = function(e) {
  var arraybuffer = xhr.response; // not responseText
  /* ... */
}
oReq.open("GET", url, true);
oReq.responseType = "arraybuffer";
oReq.send();
```
##### 1-4 监测进度

`XMLHttpRequest` 提供了各种在请求被处理期间发生的事件以供监听。这包括定期进度通知、 错误通知，等等。

支持 DOM 的 `progress` 事件监测之于 `XMLHttpRequest` 传输，遵循 `Web API` [进度事件规范](https://xhr.spec.whatwg.org/) : 这些事件实现了 [ProgressEvent](https://developer.mozilla.org/zh-CN/docs/Web/API/ProgressEvent) 接口。

##### 1-5 提交表单和上传文件

`XMLHttpRequest` 的实例有两种方式提交表单：
  >- 使用 AJAX
  >- 使用 FormData API

第二种方式（ 使用 FormData API ）是最简单最快捷的，但是缺点是被收集的数据无法使用[JSON.stringify()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)转换为一个 JSON字符串。
第一种方式反而是最复杂的但也是最灵活和最强大。

请求方式这里不做太多赘述，一个[传送门](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest#%E6%8F%90%E4%BA%A4%E8%A1%A8%E5%8D%95%E5%92%8C%E4%B8%8A%E4%BC%A0%E6%96%87%E4%BB%B6),有兴趣的小伙伴可以自己去查阅一下。

#### 二、Jquery Ajax

```javascript
$.ajax({
  url: "/api/getWeather",
  data: {
    zipcode: 97201
  },
  success: function( result ) {
    $( "#weather-temp" ).html( "<strong>" + result + "</strong> degrees" );
  }
});
```
传统 Ajax 指的是 `XMLHttpRequest（XHR）`， 最早出现的发送后端请求技术，隶属于原始js中，核心使用`XMLHttpRequest`对象，多个请求之间如果有先后关系的话，就会出现回调地狱。`Jquery Ajax`的出现是对原生XHR的封装，除此以外还增添了对JSONP的支持，`Jquery Ajax`经过多年的更新维护，真的已经是非常的方便了，但是随着`react`,`vue`,`angular`新一代框架的兴起，以及ES规范的完善，更多API的更新，它逐渐暴露了自己的不足:

> - 1.本身是针对MVC的编程,不符合现在前端MVVM的浪潮
> - 2.基于原生的XHR开发，XHR本身的架构不清晰,已经有了fetch的替代方案
> - 3.JQuery整个项目太大，单纯使用ajax却要引入整个JQuery非常的不合理（采取个性化打包的方案又不能享受CDN服务）
> - 4.不符合关注分离（Separation of Concerns）的原则
> - 5.配置和调用方式非常混乱，而且基于事件的异步模型不友好

默认情况下，`Ajax` 请求使用 `GET` 方法。如果要使用 `POST` 方法，可以设定 `type` 参数值。这个选项也会影响 data 选项中的内容如何发送到服务器。

data 选项既可以包含一个`查询字符串`，比如 `key1=value1&key2=value2` ，也可以是一个映射，比如 `{key1: 'value1', key2: 'value2'}` 。如果使用了后者的形式，则数据再发送器会被转换成查询字符串。这个处理过程也可以通过设置 `processData` 选项为 `false` 来回避。如果我们希望发送一个 `XML` 对象给服务器时，这种处理可能并不合适。并且在这种情况下，我们也应当改变 `contentType` 选项的值，用其他合适的 `MIME` 类型来取代默认的 `application/x-www-form-urlencoded` 。

```javascript
var list = {}; 
$.ajax({
    //请求方式 POST || GET
    type : "POST", 
    //请求的媒体类型
    contentType: "application/json;charset=UTF-8",
    //请求地址
    url : "http://127.0.0.1/xxxx/",
    //数据，json字符串
    data : JSON.stringify(list),
    //请求成功
    success : function(result) {
        console.log(result);
    },
    //请求失败，包含具体的错误信息
    error : function(e){
        console.log(e.status);
        console.log(e.responseText);
    }
});
```
下面的表格列出了 jQuery AJAX 方法：

| 方法 | 描述 |
| `$.ajax()` | 执行异步 AJAX 请求 |
| `$.ajaxPrefilter()` | 在每个请求发送之前且被 `$.ajax()` 处理之前，处理自定义 `Ajax` 选项或修改已存在选项 |
| `$.ajaxSetup()` | 为将来的 `AJAX` 请求设置默认值 |
| `$.ajaxTransport()` | 创建处理 Ajax 数据实际传送的对象 |
| `$.get()` | 使用 AJAX 的 `HTTP GET` 请求从服务器加载数据 |
| `$.getJSON()` | 使用 `HTTP GET` 请求从服务器加载 JSON 编码的数据 |
| `$.getScript()` | 使用 AJAX 的 HTTP GET 请求从服务器加载并执行 JavaScript |
| `$.param()` | `创建数组或对象的序列化`表示形式（可用于 AJAX 请求的 URL 查询字符串） |
| `$.post()` | 使用 AJAX 的 HTTP POST 请求从服务器加载数据 |
| `ajaxComplete()` | 规定 AJAX 请求`完成时运行`的函数 |
| `ajaxError()` | 规定 AJAX 请求`失败时运行`的函数 |
| `ajaxSend()` | 规定 AJAX 请求`发送之前运行`的函数 |
| `ajaxStart()` | 规定第一个 AJAX 请求`开始时运行`的函数 |
| `ajaxStop()` | 规定所有的 AJAX 请求`完成时运行`的函数 |
| `ajaxSuccess()` | 规定 AJAX 请求`成功完成时运行`的函数 |
| `load()` | 从服务器加载数据，并把返回的数据放置到指定的元素中 |
| `serialize()` | 编码表单元素集为`字符串`以便提交 |
| `serializeArray()` | 编码表单元素集为 `names` 和 `values` 的数组 |

对于Jquery Ajax来说我是特别的喜欢，只是人在进步，新的知识中终归会替代那些旧的。

> **总结：** 这里引用`海贼王`白胡子说的一句话，如图，加载不出来就别看了~~

#### 三、Axios

_先来看看官网的案例：_

执行 `GET` 请求
```javascript
// 为给定 ID 的 user 创建请求
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });

// 上面的请求也可以这样做
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

执行 `POST` 请求
```javascript
axios.post('/user', {
    firstName: 'Fred',
    lastName: 'Flintstone'
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```
执行多个`并发`请求
```javascript
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // 两个请求现在都执行完成
  }));
```


> Vue2.0之后，[尤雨溪](https://baijiahao.baidu.com/s?id=1637409946654270388&wfr=spider&for=pc)推荐大家用[axios](http://www.axios-js.com/zh-cn/docs/)`替换`JQuery ajax`，未来App的趋势是轻量化和细化，能解决问题的应用就是好应用,想必让Axios进入了很多人的目光中。Axios本质上也是对原生XHR的封装，只不过它是Promise的实现版本，可以用在浏览器和 node.js 中,符合最新的ES规范，从它的官网上可以看到它有以下几条特性：

- 从浏览器中创建 [XMLHttpRequests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
- 从 node.js 创建 http 请求
- 支持 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) API
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 XSRF

> `XSRF`（**Cross Site Request Forgery**, 跨站域请求伪造）也称 XSRF， 是一种网络的攻击方式，它在 2007 年曾被列为互联网 20 大安全隐患之一。其他安全隐患，比如 SQL 脚本注入，跨站域脚本攻击等在近年来已经逐渐为众人熟知，很多网站也都针对他们进行了防御。然而，对于大多数人来说，CSRF 却依然是一个陌生的概念。即便是大名鼎鼎的 Gmail, 在 2007 年底也存在着 CSRF 漏洞，从而被黑客攻击而使 Gmail 的用户造成巨大的损失。`客户端支持防御 XSRF`，是怎么做到的呢，就是让你的每个请求都带一个从`cookie`中拿到的`key`, 根据浏览器同源策略，假冒的网站是拿不到你`cookie`中得`key`的，这样，后台就可以轻松辨别出这个请求是否是用户在假冒网站上的误导输入，从而采取正确的策略。

axios创建请求时可以用的配置选项。只有 url 是必需的。如果没有指定 method，请求将默认使用 get 方法。[请求配置传送门](http://www.axios-js.com/zh-cn/docs/#%E8%AF%B7%E6%B1%82%E9%85%8D%E7%BD%AE)

`Axios`既提供了并发的封装，体积也较小，也没有下文会提到的`fetch`的各种问题，当之无愧是现在最应该选用的请求的方式。

#### 四、Fetch
> `Fetch` 提供了对 `Request` 和 `Response` （以及其他与网络请求有关的）对象的通用定义。
> `Fetch` 是一个现代的概念, 等同于 `XMLHttpRequest`。它提供了许多与XMLHttpRequest相同的功能，但被设计成更具可扩展性和高效性。

`Fetch API` 提供了一个 `JavaScript`接口，用于访问和操纵`HTTP管道`的部分，例如请求和响应。它还提供了一个全局 `fetch()`方法，该方法提供了一种简单，合理的方式来跨网络异步获取资源。

请注意，fetch规范与jQuery.ajax()主要有两种方式的不同，牢记：

- 当接收到一个代表错误的 HTTP 状态码时，从 fetch()返回的 `Promise` 不会被标记为 `reject`， 即使该 HTTP 响应的状态码是 `404` 或 `500`。相反，它会将 `Promise` 状态标记为 `resolve` （`但是会将 resolve 的返回值的 ok 属性设置为 false` ），仅当网络故障时或请求被阻止时，才会标记为 `reject`。
- 默认情况下，`fetch` 不会从服务端发送或接收任何 `cookies`, 如果站点依赖于用户 `session`，则会导致未经认证的请求（要发送 `cookies`，必须设置 `credentials` 选项）。自从2017年8月25日后，默认的credentials政策变更为`same-originFirefox`也在61.0b13中改变默认值

**一个基本的 fetch请求设置起来很简单。看看下面的代码：**
```javascript
fetch('http://example.com/movies.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson);
  });
```
这里我们通过网络获取一个`JSON文件`并将其打印到控制台。最简单的用法是只提供一个参数用来指明想`fetch()`到的资源路径，然后返回一个包含响应结果的`promise(一个 Response 对象)`。

当然它只是一个 HTTP 响应，而不是真的JSON。为了获取JSON的内容，我们需要使用  `json()方法`（在Bodymixin 中定义，被 Request 和 Response 对象实现）。

**fetch() 接受第二个可选参数，一个可以控制不同配置的 init 对象：**
```javascript
// Example POST method implementation:

postData('http://example.com/answer', {answer: 42})
  .then(data => console.log(data)) // JSON from `response.json()` call
  .catch(error => console.error(error))

function postData(url, data) {
  // Default options are marked with *
  return fetch(url, {
    body: JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    },
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
  })
  .then(response => response.json()) // parses response to JSON
}
```
`fetch的优点：`

- 1.语法简洁，更加语义化
- 2.基于标准 Promise 实现，支持 async/await
- 3.同构方便，使用 [isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch)
- 4.更加底层，提供的API丰富（request, response）
- 5.脱离了XHR，是ES规范里新的实现方式

**`fetch`在前端的应用上有一项xhr怎么也比不上的能力：跨域的处理**

我们都知道因为同源策略的问题，浏览器的请求是可能随便跨域的——一定要有跨域头或者借助JSONP，但是，fetch中可以设置`mode为"no-cors"`（不跨域），如下所示：
```javascript
fetch('/users.json', {
    method: 'post', 
    mode: 'no-cors',
    data: {}
}).then(function() { /* handle response */ });
```
这样之后我们会得到一个type为“opaque”的返回。需要指出的是，这个请求是真正抵达过后台的，所以我们可以使用这种方法来进行信息上报，在我们之前的image.src方法中多出了一种选择，另外，我们在network中可以看到这个请求后台设置跨域头之后的实际返回，有助于我们提前调试接口（当然，通过chrome插件我们也可以做的到）。



> 参考资料[Fetch_API](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API)

> 参考资料[MDC AJAX introduction](https://developer.mozilla.org/zh-CN/docs/Web/Guide/AJAX/Getting_Started)

> 参考来源于[MDN](https://developer.mozilla.org/zh-CN/) 

