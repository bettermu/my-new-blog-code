---
title: 浅析DOM事件
date: 2018-06-22 20:28:41
tags:
  - js
  - DOM
categories:
  - js
---



## 一、理解事件  

* 事件行为本身：没有给事件绑定方法事件也是一直存在的，当触发行为的时候，也对触发对应的行为，只不过由于没有绑定事件，导致没有任何事件发生；

* 事件绑定：给元素绑定一个方法；触发行为，执行方法；  

## 二、DOM事件  

* DOM0级事件  

  * (onclick)属于元素的私有属性  

  * 使用DOM0级方法指定的事件处理程序被认为是元素的方法，这也解释了为什么事件处理程序的this指向当前的元素  

  * 事件处理程序只会在事件冒泡阶段处理  

  * 优势：简单、跨浏览器  

  * 删除事件：设置为null，即 btn.onclick=null  

  * 一个元素只能绑定一个同类型的行为，否则后面的会覆盖前面的行为  


* DOM1：没有升级事件相关的方法  

* DOM2级事件  

  * 属于公有方法，在eventTarget这个类原型上，所有的COM节点都包含这两种方法：addEventListener和removeEventListener  

  * 三个参数：(事件名，回调函数，布尔值)；布尔值为true，表示捕获阶段调用回调函数，布尔值为false,表示冒泡阶段调用回调函数，默认值为false  

  * addEventListener和removeEventListener传入的回调函数必须相同，不能使用匿名函数  

  * 一般将事件添加到冒泡阶段，这样可以最大限度的兼容浏览器  

  * 同一个元素可以绑定多个统一的行为，触发顺序以绑定顺序先后为准  


## 事件对象

* 执行某一行为时，不仅执行了绑定方法，而且浏览器还会默认的给这个方法传递一个参数，这个参数就是event，事件对象  

* 事件对象的特点：  

  * 对象数据类型，包含很多的属性名和属性值，用来记录行为的相关信息  

  * MouseEvent---UIEvent---Event---Object 原型  

  * MouseEvent记录的是页面中唯一一个鼠标每次触发的相关信息，和到底在哪个元素上触发没有关系。  


## 事件对象的兼容性

* 事件对象本身的兼容性问题：  
```js
e = e || window.event* 
```

* e.type:当前行为类型，兼容  

* e.clientX/Y:距离可视窗口左上角x,y值，兼容；  

* e.pageX/Y:距离body(第一屏)左上角x,y值，IE678不兼容；  

```js
e.pageX=(document.documentElement.scrollLeft||document.body.scrollLeft)+ e.clientX;
```

* e.target:事件源，当前行为触发元素，存储的就是那个元素IE678不兼容 e.target=e.target||e.srcElement;  

* e.preventDafault:阻止浏览器的默认行为，IE678不兼容；  

```js
e.preventDafault?e.preventDafault:e.returnvalue=false;
```

* e.stopPropagation:阻止事件冒泡传播，IE678不兼容； 

```js
e.stopPropagation? e.stopPropagation:e.cancelBubble=true;
```

## 5.事件的传播机制

* 捕获：从外向内依次查找元素，event capturing；

* 目标：当前述事件源本省的操作；

* 冒泡：从内到外依次触发的相关行为，event bubbing;

