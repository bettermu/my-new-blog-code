---
title: 交叉观察者IntersectionObserver介绍
date: 2019-09-20 21:08:19
tags:
  - IntersectionObserver
  - 浏览器API
categories:
  - IntersectionObserver
  - BOM
---


## 介绍

> 关于IntersectionObserver，MDN里是这么介绍的:

> IntersectionObserver接口，提供了一种异步观察目标元素与其祖先元素或者顶级文档视窗(viewport)交叉状态的方法，祖先元素与视窗被称为根(root)

> 通过上面的介绍，我们大概知道了IntersectionObserver翻译为“交叉观察者”，它的任务就是监听目标元素跟指定父元素(若未指定，则默认为视窗)是否发生交叉行为，简单理解就是监听目标元素是否进入或者离开了指定父元素的内部。

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/1.png?raw=true)

## 用法

### 1、构造函数
```js
new IntersectionObserver(callback,options);
```
### 2、callback

> 发生交叉的回调，接受一个entries参数，返回当前已监听并且发生了交叉的目标集合

```js
new IntersectionObserver(entries => {
    entries.forEach(item => console.log(item))
},options);
```
> 下面介绍下item里包含的常用属性：

| 属性        | 说明    |
| :--------   | :-----  |
| boundingClientRect        | 元素空间信息      | 
| intersectionRatio        | 元素可见区域的占比      |  
| isIntersecting        | 字面理解为是否正在交叉，可用做判断元素是否可见      |   
|target | 目标节点，与event,target类似 |

> 这里需要注意的是，页面初始化的时候，会触发一次callback，其中的entries为所有已监听的目标集合

### 3、options

> 顾名思义，他是一个配置参数，对象类型，非必填，常用属性如下：

|属性|说明|
|:---------|:---|
|root|指定父元素，默认为视窗viewport|
|rootMargin|触发交叉的偏移值，默认为“0px 0px 0px 0px”(上左下右，正数为向外扩散，负数则向内收缩)|

```js
new IntersectionObserver(callback, {
  root: document.querySelector("xx"),
  rootMargin: "0px 0px -100px 0px"
});
```
> 如果设置rootMargin为"20px 0px 30px 30px",相当于范围相对于视窗外扩，如下图，那么元素未达到视窗时，就已经切换为可见状态了:

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/2.png?raw=true)

### 4、常用方法
|名称|说明|参数|
|:---|:---|:---|
|observe|开始监听一个目标元素|节点|
|unobserve|停止监听一个目标元素|节点|
|takeRecords|返回所有监听的目标元素集合||
|disconnect|停止所有监听||

## 例子
### 1. 假设页面上有一个class="box"的盒子且父元素为视窗：

```js
let box = document.querySelector(".box");

let observer = new IntersectionObserver(entries => {
  entries.forEach(item => {
    let tips = item.isIntersecting ? "进入了父元素的内部" : "离开了父元素的内部";
    console.log(tips);
  });
});

observer.observe(box); // 监听一个box

```

> 效果如下：       

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/3.gif?raw=true)

### 2. 假设页面上有多个class="box"的盒子且父元素为视窗：

```js
let box = document.querySelectorAll(".box");

let observer = new IntersectionObserver(entries => console.log(`发生交叉行为，目标元素有${entries.length}个`));

box.forEach(item => observer.observe(item)); // 监听多个box

```

> 当所有盒子距离视窗顶部距离一致时，效果如下:

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/4.gif?raw=true)

> 当所有盒子距离视窗顶部距离不一致时，效果如下:

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/5.gif?raw=true)

> 关于举例上面两种情况，因为entries是返回当前已监听并且发生了交叉的目标集合，第一种情况是大家一起发生了交叉，因此每次返回的长度为3，第二种情况则是每个目标轮流发生交叉，且每次只触发一个，因此集合每次返回的长度为1。

### 3. 指定父元素

> 假设html如下:

```html
<div class="parent">
  <div class="child"></div>
</div>
```

> 然后开始监听：

```js
let child = document.querySelector(".child");

let observer = new IntersectionObserver(entries => {
  entries.forEach(item => {
    console.log(item.isIntersecting ? "可见" : "不可见");
  });
}, {
  root: document.querySelector(".parent")
});

observer.observe(child); // 开始监听child

```

> 效果如下：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/6.gif?raw=true)

## 实际应用

### 1、图片懒加载

> 以前我们做图片懒加载，都是监听浏览器的滚动，然后遍历拿到每个图片的空间信息，然后判断一些位置信息，从而进行图片加载；而现在，只需要交给交叉观察者去做：

> 假设html结构如下：

```html
// 多个
<img src="" data-origin="图片链接">
```

> 然后开始监听：

```js
let images = document.querySelectorAll("img.lazyload");

let observer = new IntersectionObserver(entries => {
  entries.forEach(item => {
    if (item.isIntersecting) {
      item.target.src = item.target.dataset.origin; // 开始加载图片
      observer.unobserve(item.target); // 停止监听已开始加载的图片
    }
  });
});

images.forEach(item => observer.observe(item));

```

> 效果如下：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/8.gif?raw=true)

> 把网速调慢：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/7.gif?raw=true

> 当滚动条为横向滚动的时候，我们依然可以进行懒加载的监听：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/9.gif?raw=true)

> 传统的懒加载只是监听全局滚动条的滚动，像这种小细节还是无法实现的（传统的实现方法并不是判断目标是否出现在视窗，所以横向的图片会一起加载，即使你没有向左滑动），所以这也是交叉观察者的一大优点

### 2、触底

> 触底加载的方式，在移动端列表的场景中十分常见，通常我们是通过监听列表本身的滚动距离与列表高度和视口高度的视差进行比较，从而判断是否触底。但是有了交叉观察者之后，我们的做法就很简单了。

> 我们可以在列表底部，放置一个参照元素，我们让交叉观察者去监听：

> html结构如下:

```html
<!-- 数据列表 -->
<ul>
  <li>index</li>
</ul>

<!-- 参照元素 -->
<div class="reference"></div>
```

> 然后监听参照元素：

```js
new IntersectionObserver(entries => {
  let item = entries[0]; // 拿第一个就行，反正只有一个
  if (item.isIntersecting) console.log("滚动到了底部，开始请求数据");
}).observe(document.querySelector(".reference")); // 监听参照元素

```

> 效果如下：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/10.gif?raw=true)

### 3、吸顶

> 实现元素吸顶的方式有很多种，如css的position: sticky，兼容性较差；如果用交叉观察者实现也很方便，同样也要放一个参照元素；

> 假设html结构如下：

```html
<!-- 参照元素 -->
<div class="reference"></div>

<nav>我可以吸顶</nav>
```

> 假设scss代码如下：

```scss
nav {
  &.fixed {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
  }
}
```

> 开始监听：

```js
let nav = document.querySelector('nav');
let reference = document.querySelector(".reference");

new IntersectionObserver(entries => {

  let item = entries[0];
  let top = item.boundingClientRect.top;

  // 当参照元素的的top值小于0，也就是在视窗的顶部的时候，开始吸顶，否则移除吸顶
  if (top < 0) nav.classList.add("fixed");
  else nav.classList.remove("fixed");

}).observe(reference);

```

> 效果如下:

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/11.gif?raw=true)

> 如果这么简单就好了，这样会有个问题，如果滚的慢的时候，会掉进一个死循环：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/12.gif?raw=true)

> 为了方便观察，我们把参考元素加一个高度跟颜色：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/13.gif?raw=true)

> 看图，问题很明显，当给nav增加fixed定位的时候，nav脱离了文档流，因此参考元素会往下掉，然后就会又发生交叉，从而去除了fixed定位，陷入一个死循环......

> 解决方式是，让参考元素脱离文档流，可以让它绝对定位至nav的上方：

```js
let nav = document.querySelector('nav');
let reference = document.querySelector(".reference");

reference.style.top = nav.offsetTop + "px";

// 以下代码不变 ...

```

> 效果如下

![](https://github.com/bettermu/blog-picture-store/blob/master/20190921/14.gif?raw=true)


## 兼容性

> IE不兼容，不过有[官方的polyfill](https://github.com/w3c/IntersectionObserver/tree/master/polyfill)
