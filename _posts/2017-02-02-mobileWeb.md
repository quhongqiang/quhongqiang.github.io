---
layout: post
header-img: "img/tag-bg.jpg"
header-mask: 0.4
author: "曲小强"
title: 移动端页面开发遇到的一些问题
subtitle: 开发移动端的时候总会遇到一些千奇百怪的小问题，问题不大，但是不解决又不行，这里罗列了一些我遇到的问题，提出来与大家分享一下。
catalog: true
music-id: 528658316
auto: 1
tags:
  - 移动端
---

### 一、meta 标签的相关知识

1、移动端页面设置**视口宽度**等于**设备宽度**，并**禁止缩放**。

```html
<meta
  name="viewport"
  content="width=device-width,
              initial-scale=1.0"
/>
```

2、移动端页面设置**视口宽度**等于**设备宽度**，并**禁止缩放**，常用于微信浏览器页面。

```html
<meta
  name="viewport"
  content="width=device-width,
              initial-scale=1.0,
              minimum-scale=1.0,
              maximum-scale=1.0,
              user-scalable=no"
/>
```

3、禁止将页面中的**数字识别为电话号码**

```html
<meta name="format-detection"
      content="telephone=no" />
```

4、忽略 Android 平台中对**邮箱地址**的识别

```html
<meta name="format-detection"
      content="email=no" />
```

5、当网站添加到主屏幕**快速启动方式**，可隐藏地址栏，仅针对 `ios 的 safari`

```html
<meta name="apple-mobile-web-app-capable"
      content="yes" />
<!-- ios7.0版本以后，safari上已看不到效果 -->
```

6、将网站添加到主屏幕快速启动方式，仅针对 `ios 的 safari` 顶端状态条的样式

```html
<meta name="apple-mobile-web-app-status-bar-style"
      content="black" />
<!-- 可选default、black、black-translucent -->
```

**viewport 模板**

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="width=device-width,
              initial-scale=1.0,
              maximum-scale=1.0,
              user-scalable=no"
    />
    <meta content="yes" 
          name="apple-mobile-web-app-capable" />
    <meta content="black" 
          name="apple-mobile-web-app-status-bar-style" />
    <meta content="telephone=no"
          name="format-detection" />
    <meta content="email=no"
          name="format-detection" />
    <title>title</title>
    <link rel="stylesheet" href="index.css" />
  </head>
  <body>
    content...
  </body>
</html>
```

### 二、CSS 的样式技巧

1、禁止 `ios` 和 `android` 用户选中文字
```css
.css { -webkit-user-select:none }
```
2、禁止 ios 长按时触发系统的菜单，禁止 `ios & android` 长按时下载图片
```css
.css { -webkit-touch-callout: none }
```
3、`webkit` 去除表单元素的默认样式
```css
.css { -webkit-appearance:none; }
```
4、修改 `webkit` 表单输入框 `placeholder` 的样式
```css
input::-webkit-input-placeholder{
  color: #aaa;
}

input:focus::-webkit-input-placeholder{
  color: #eee;
}
```
5、去除 `android a/button/input` 标签被点击时产生的**边框** & 去除 `ios a` 标签被点击时产生的半透明灰色背景
```css
a,button,input {
  -webkit-tap-highlight-color:rgba(255,0,0,0);
}
```
6、ios 使用 `-webkit-text-size-adjust` 禁止调整字体大小
```css
body {
  -webkit-text-size-adjust: 100%!important;
}
```
7、`android` 上去掉语音输入按钮
```css
input::-webkit-input-speech-button {
  display: none;
}
```
8、移动端定义字体，移动端没有微软雅黑字体
```css
/* 移动端定义字体的代码 */ 
body {
  font-family:Helvetica;
}
```
### 三、其他问题

1、手机拍照和上传图片
```html
<!-- 选择照片 -->
<input type=file accept="image/*">

<!-- 选择视频 -->
<input type=file accept="video/*">
```
2、取消 `input` 在ios下，输入的时候英文首字母的默认大写
```html
<input autocapitalize="off" 
       autocorrect="off" />
```
3、打电话和发短信
```html
<a href="tel:0100-100000">打电话给:0100-100000</a>

<a href="sms:10086">发短信给: 10086</a>
```
### 四、CSS reset

```css
@charset "utf-8";
html{
  -webkit-text-size-adjust:none;
  -webkit-user-select:none;
  -webkit-touch-callout: none;
  font-family: Helvetica;
}
body{font-size:12px;}
body,h1,h2,h3,h4,h5,h6,
p,dl,dd,ul,ol,pre,form,
input,textarea,th,td,select{
  margin:0; 
  padding:0; 
  font-weight: normal;
  text-indent: 0;
}
a,button,input,
textarea,select{ 
  background: none; 
  -webkit-tap-highlight-color:rgba(255,0,0,0); 
  outline:none; 
  -webkit-appearance:none;
}
em{
  font-style:normal
}
li{
  list-style:none
}
a{text-decoration:none;}
img{
  border:none; 
  vertical-align:top;
}
table{
  border-collapse:collapse;
}
textarea{ 
  resize:none; overflow:auto;
}
 
```
`Normalize.css`是一种 `CSS reset`的替代方案。它在默认的 `HTML` 元素样式上提供了跨浏览器的高度一致性。相比于传统的`CSS reset`，`Normalize.css`是一种现代的、为HTML5准备的优质替代方案。

[Normalize.css的传送门](https://blog.csdn.net/quhongqiang/article/details/79925955)

### 五、常用公用 CSS style
```css
/* 清除浮动 */
.clear { zoom:1; }
.clear:after { 
  content: ''; 
  display: block;
  clear: both;
}
 
/* 定义盒模型为怪异和模型（宽高不受边框影响） */
.boxSiz{
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  -ms-box-sizing: border-box;
  -o-box-sizing: border-box;
  box-sizing: border-box;
}
 
/* 强制换行 */
.toWrap{
  /* 只对英文起作用，以字母作为换行依据。 */
  word-break: break-all;
  /* 只对英文起作用，以单词作为换行依据。*/
  word-wrap: break-word;
  /* 只对中文起作用，强制换行。*/
  white-space: pre-wrap;
}
 
/* 禁止换行 */
.noWrap{
  white-space: nowrap;
}
 
/* 禁止换行,超出省略号 */
.noWrapEllipsis{
  white-space: nowrap; 
  overflow: hidden; 
  text-overflow: ellipsis;
}
 
/* 文字两端对齐 */
.text-justify{
  text-align: justify;
  text-justify: inter-ideograph;
}
 
/* 定义盒模型为 flex布局兼容写法并让内容水平垂直居中 */
.flex-center{
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -o-box;
  display: box;
  -webkit-box-pack: center;
  -moz-box-pack: center;
  -ms-flex-pack: center;
  -o-box-pack: center;
  box-pack: center;
  -webkit-box-align: center;
  -moz-box-align: center;
  -ms-flex-align: center;
  -o-box-align: center;
  box-align: center;
}
```

### 六、flex弹性布局

1、定义弹性盒模型兼容写法
```css
/*
  box
  inline-box
*/
.css {
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -o-box;
  display: box;
}
```
2、`box-orient` 定义盒模型内伸缩项目的布局方向
```css
/**
* vertical column 垂直
* horizontal row 水平 默认值
*/
.css {
  -webkit-box-orient: horizontal;
  -moz-box-orient: horizontal;
  -ms-flex-direction: row;
  -o-box-orient: horizontal;
  box-orient: horizontal;
}
```

3、`box-direction` 定义盒模型内伸缩项目的正序(normal默认值)、倒叙(reverse)
```css
/* Firefox */
.css {
  display: -moz-box;
  -moz-box-direction: reverse;
}
 
/* Safari、Opera 以及 Chrome */
.css {
  display: -webkit-box;
  -webkit-box-direction: reverse;
}
```

4、box-pack 对盒子水平富裕空间的管理
```css
/*
  start
  end
  center
  justify
*/
.css {
  -webkit-box-pack: center;
  -moz-box-pack: center;
  -ms-flex-pack: center;
  -o-box-pack: center;
  box-pack: center;
}
```

5、box-pack 对盒子垂直方向富裕空间的管理
```css
/*
  start
  end
  center
*/
/* box-align */
.css {
  -webkit-box-align: center;
  -moz-box-align: center;
  -ms-flex-align: center;
  -o-box-align: center;
  box-align: center;
}

```
6、定义伸缩项目的具体位置
```css
/*-moz-box-ordinal-group:1;*/    /* Firefox */
/*-webkit-box-ordinal-group:1;*/ /* Safari 和 Chrome */
 
.box div:nth-of-type(1){
  -webkit-box-ordinal-group:1;
}
 
.box div:nth-of-type(2){
  -webkit-box-ordinal-group:2;
}
 
.box div:nth-of-type(3){
  -webkit-box-ordinal-group:3;
}
 
.box div:nth-of-type(4){
  -webkit-box-ordinal-group:4;
}
 
.box div:nth-of-type(5){
  -webkit-box-ordinal-group:5;
}
```

7、定义伸缩项目占空间的份数
```css
/* -moz-box-flex: 2.0;     Firefox */
/* -webkit-box-flex: 2.0;  Safari 和 Chrome */

.css {
  .box div:nth-of-type(1){
    -webkit-box-flex:1;
  }
 
  .box div:nth-of-type(2){
    -webkit-box-flex:2;
  }
  
  .box div:nth-of-type(3){
    -webkit-box-flex:3;
  }
  
  .box div:nth-of-type(4){
    -webkit-box-flex:4;
  }
  
  .box div:nth-of-type(5){
    -webkit-box-flex:5;
  }
}

```
> [移动web问题整理](https://juejin.im/post/5d3acb42f265da1b7638e8fc)

欢迎留言 ~~

![](https://github.com/quhongqiang/quhongqiang.github.io/blob/master/img/_posts/17.png?raw=true)