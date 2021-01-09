---
title: 'ios移动端部分手机不支持background-attachment: fixed 的解决办法'
date: 2018-04-30 11:08:52
tags:
  - 移动端页面
  - css3
categories:
  - css
---

之前在做的微信端相关页面开发的时候，在一些必要的需求场景下，ios系统和某些移动端background-attachment:fixed不兼容性，并不能够达到背景图不随滚动条而滚动的效果，随后在网上查找了相关的解决方案，经过实际测试，下面的hack是管用的，也就是在设置背景图的元素上面，加一个伪元素的样式表，如下：

```css
  (背景图所在的元素):before {
  content: ' ';
  position: fixed;
  z-index: -1;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background: url(path/to/image) center 0 no-repeat;
  background-size: cover;
}
```

亲测有效，遇到相关问题的童鞋，可以尝试一下这种解决方案。巧妙又方便。