---
title: css相关技巧总结(二)
date: 2018-06-19 20:07:09
tags:
  - css
categories:
  - css
---


继之前给大家分享的css相关技巧总结，这次带来了一些不一样的css技巧，这些技巧属于当我们无法通过普通的方式去解决一些需求问题的时候，另辟蹊径，达到的与原需求差不多的实现。

## 一、CSS写自适应大小的正方形
**代码**：
```css
/*以图片为例*/

/*background 写法*/

.img {
  width:100%;
  height:0;
  padding-bottom:100%;       /*关键所在*/
  overflow:hidden;
  background:url(../res/images/haha.png) center/100% 100% no-repeat;
}
.img img{
  width: 100%;
}

/*img 写法*/

.img {
  position:relative;
  width:100%;
  height:0;
  padding-bottom:100%;       /*关键所在*/
  overflow:hidden;
}
.img img {
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
```
**效果图**：

![](https://github.com/bettermu/blog-picture-store/blob/master/20180619/%E8%87%AA%E9%80%82%E5%BA%94%E5%A4%A7%E5%B0%8F%E7%9A%84%E6%AD%A3%E6%96%B9%E5%BD%A2.gif?raw=true)

**原理**：

元素的padding的百分比值四个值均根据当前元素的宽度来计算的
padding只能取top或者bottom，自适应正方形其值要和宽一致，那么自然如果需要用到不同比例的矩形，可以通过设置不同比例的padding就能得到。

## 二、多列等高
**代码**：
```css
.web_width {
  width:100%;
  overflow:hidden;     /*关键所在*/
}

.left {
  float:left;
  width:20%;
  min-height:10rem;
  background:#66afe9;
  padding-bottom:2000px;     /*关键所在*/
  margin-bottom:-2000px;     /*关键所在*/
}

.right {
  float:right;
  width:80%;
  height:20em;
  background:#f00;
}
```
**效果图**：

![](https://github.com/bettermu/blog-picture-store/blob/master/20180619/%E5%A4%9A%E5%88%97%E7%AD%89%E9%AB%98.gif?raw=true)

**原理**：

padding补偿法

在高度小的元素上加一个数值为正的padding-bottom和一个数值为负的margin-bottom，再在父级加上overflow:hidden隐藏子元素超出的padding-bottom

**注意**：  
padding-bottom和margin-bottom之和要等于0，而且建议值不要太大，够用就可以了。



## 三、隐藏滚动条
这个需求其实真的是，个人觉得其实实现价值并不太高，无奈，在之前项目里，有一个需求就是这样的，页面有一个固定高度的展示框，当内容超过高度的时候，希望将滚动条隐藏，是的，不是美化，而是隐藏，仔细想了下，可以在容器外再套一层容器，该容器负责隐藏掉子容器的滚动条：

**代码**：
```html
<style type="text/css">
* {
  margin: 0;
  padding: 0
}
	
section {
  width: 300px;
  height: 500px;
  margin: 20px auto;
  overflow: hidden;
}
	
div {
  width: calc(100% + 20px);
  height: 100%;
  overflow-x: hidden;
  overflow-y: auto;
}
	
p {
  width: 100%;
  height: 200px;
  background: #999;
  overflow: hidden
}
p:nth-child(2n){
  background: #f60;
}
</style>
<section>
  <div>
    <p>1</p>
    <p>2</p>
    <p>3</p>
    <p>4</p>
    <p>5</p>
  </div>
</section>
```

**效果图**：

![](https://github.com/bettermu/blog-picture-store/blob/master/20180619/%E9%9A%90%E8%97%8F%E6%BB%9A%E5%8A%A8%E6%9D%A1.gif?raw=true)

**原理**：

父元素超出部分隐藏，将滚动元素的width超出父元素的width，从而达到隐藏滚动条
