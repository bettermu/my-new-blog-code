---
title: '解决transition过度在height:auto失效的问题'
date: 2019-06-25 21:54:33
tags:
  - css
  - transition
categories:
  - css
  - transition
---

最近项目中遇到了一个问题，竖向菜单显示和隐藏，通过transition过渡 影响height改变，本来代码如下：

```css

.demo {
  height:0;
  transition:all .5s
}
.demo.active {
  height:auto
}
```

上面的代码，所处的场景是，过渡元素的高度是不确定的，因此需要从height:0 到 height:auto 过渡，但是实际上是无法实现效果的，原因是并不是所有的css属性都支持过渡，支持过渡的前提是，其属性有明确的开始值，和结束值，并且存在过渡的中间值，如果我们高度不确定，那么过渡效果是无法实现的，那么解决办法是什么呢？

css还有一个属性，max-height，顾名思义，就是当元素高度没有该值高时，以该元素本身高度展示，当元素高于该值时，则高度以该值显示，我们给该元素加上一个足够高的max-height属性，那么，我们就能通过max-height属性，来对元素进行过渡啦：


```css

.demo {
  max-height:0;
  transition:all .5s
}
.demo.active {
  max-height:200px; /*可根据实际情况设置高度值*/
}
```

上面的代码，就能够实现高度的过渡效果了。

