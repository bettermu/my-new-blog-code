---
title: 解决js中onclick与ondblclick事件绑定冲突的问题
date: 2018-06-27 22:22:53
tags:
  - 原生js
  - 事件绑定
categories:
  - js
---

最近在黄山学院智慧旅游语音导览系统里，采用了百度地图的API，其中给自定义覆盖物绑定的相关事件，是采用dom0级的事件绑定，根据需求，覆盖物的单击双击事件需要触发不同的交互，所以想当然的写出了下面的代码：
```js
div.onclick=function(){
  //do something
  console.log(111)
}

div.ondblclick=function(){
  //do another something
  console.log(222)
}
```

但实际测试过程中,单击事件的绑定，成功console出了111，但是当我双击的时候，单击事件也出发了，随后双击事件也触发了，所以控制台console出了一次111，一次222。   

如果想要解决这个问题，其实很简单，只要在单击事件里加一个时间延迟，超过这个时间的，就算单击事件，如果在这个时间内又触发了，算是双击事件，时间值给300ms比较适合，所以，解决的方法如下：

```js
//定时器对象
var clickTimer = null;

div.onclick = function () {
  
  if (clickTimer) {
    window.clearTimeout(clickTimer);
    clickTimer = null;
  }

  //单击事件延迟300ms后触发
  clickTimer = window.setTimeout(function () {
    // your click process code here  
    console.log(111);
  }, 300);

}

div.ondblclick = function () {
  
  //进入双击后，取消定时器，从而阻止单击事件的触发
  if (clickTimer) {
    window.clearTimeout(clickTimer);
    clickTimer = null;
  }

  // your click process code here  
  console.log(222)
}
```

所以，处理思想就是：利用定时器延迟执行onclick事件，这样在双击过程中会取消中途触发的单击事件。
