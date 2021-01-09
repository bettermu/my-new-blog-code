---
title: max-width在单行文字中的小技巧
date: 2018-07-13 22:14:27
tags:
  - css
categories:
  - css  
  - 页面布局
---


最近的项目里，有个在百度地图上绘制相关地标的需求，后台回过来的相关数据如图所展示：  

![](https://github.com/bettermu/blog-picture-store/blob/master/20180713/1.png?raw=true)

如上图所展示的那样，后台回过来的标题字段长度是不一样的，过长的需要进行截取显示，正常的单行文字截取，样式就不再提了，在元素定宽的前提下，也就简单的这几样：
```css
ele {
  overflow: hidden;
  text-overflow:ellipsis;
  white-space: nowrap;
}

```


![](https://github.com/bettermu/blog-picture-store/blob/master/20180713/2.png?raw=true)

就可以实现单行文字超出截断的效果了，但是呢，有个问题，如果字数少于规定的界限值呢？那么，在定宽元素下，展示，肯定会露出如上图所示的，大片空白区域，那这样的展示效果，肯定是不太好的，那么在width定宽，限定太死的情况下，那么只能采用max-width来解决了。

![](https://github.com/bettermu/blog-picture-store/blob/master/20180713/3.png?raw=true)

顾名思义，max-width的意思也就是，在width不超过某值的区间内，元素宽度完全是由内部元素撑开，也就是自适应的，当元素达到max-width限定值之后，就开始以设定值固定宽度，那么设置的截断属性，也就自然而然的开始生效了，最终效果如下图所示：

![](https://github.com/bettermu/blog-picture-store/blob/master/20180713/4.png?raw=true)

最终，当文字内容没超出规定值的时候，元素宽度根据内容自适应展示，文字内容超出，则正常截断展示，提高了页面的兼容友好的展示性。
