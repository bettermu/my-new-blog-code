---
title: 关于移动端click事件延迟
date: 2018-06-14 21:18:24
tags:
  - js
categories:
  - Mobile-click
---



在自己刚接触移动端页面开发的时候，就在网上听过一些前辈口耳相传的，移动端不能使用click，因为click会比较迟钝，能用touchstart还是用touchstart。但是在自己实际的项目开发中，使用touchstart会出现一个问题，就是当用户在滑动页面的时候，不小心碰到了被绑定该事件的元素，也会导致触发touchstart事件。所以，两者都有缺点，那又如何去解决呢？   

首先，移动端的click为什么会存在迟钝现象呢，从谷歌的开发者文档《300ms tap delay,gone away》里可以找到答案：里面说，移动端存在要判断是否是双击的行为，所以单击之后，并不会马上触发click，要等300ms，直到确认不是双击了才触发click，所以也就导致了click有延迟。但是，更为重要的一点，里面也提到了，chrome32版本的已经把这个延迟去掉了，也就是2014年，呵呵，14年我还在上学呢，意思就是说，如果页面存在这么一个meta标签：
```html
<meta name="viewport" content="width=device-width">
```
即把viewport设置为设备的实际像素，那就不会有这300ms的延迟，并且，这个举动收到了IE/Firefox/Safari(iOS9.3)的支持，也就是说，现在的移动端开发，可以不用顾虑click会比较迟钝的问题了。   

但是呢，如果设置initial-scale=1.0,在chrome上面是可以生效的，但是Safari不行，如下代码：
```html
<meta name="viewport" content="initial-scale=1.0">
```

那么还有一种解决办法，就是设置CSS，如下代码：
```css
html {
  touch-action:manipulation;
}
```

这样也可以取消300ms的延迟，Chrome和Safari都可以生效了。
