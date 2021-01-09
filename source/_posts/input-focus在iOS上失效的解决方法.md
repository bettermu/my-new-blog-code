---
title: input focus在iOS上失效的解决方法
date: 2018-06-11 22:20:00
tags:
  - js 
categories:
  - input
---

之前在移动端页面的开发中，遇到个表单类的问题，项目需求是这样的，希望用户在漏填某一项的时候，input框自动聚焦，这样能友好的提醒用户，有漏填的选项，但是在实际的实现过程中，按如下的解决方法:   
```js
var apple = document.getElementById('abc');
apple.focus();
```

就可以将焦点聚焦到输入框上，但是在iOS上不行，经过相关的查阅和尝试，发现，只有通过绑定在事件上的函数触发，才能聚焦，解决方案如下：  
```js
var apple = document.getElementById('abc');
button.addEventListener('click',function(){
 apple.focus();
});
```

注意的一点是，不能将apple.focus()封装在函数中再由事件触发，这样也会失效。
