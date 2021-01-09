---
title: flex元素定宽
date: 2018-07-24 21:30:02
tags:
  - flex
  - css
categories:
  - css
  - flex
---

最近项目开发中，移动端尝试了flex布局，遇到了一个小细节：   

flex 内部元素如何定宽展示

注意的一点  如果直接给元素加上width属性，是不会生效的，原因是由于flex属性是个复合属性，如下的设置，是等同的
``` css

el {
  flex:0 0 90px
}

el {
  flex-grow:0;
  flex-shrink:0;
  flex-basis:90px;
}
```

如上代码所示，flex属性有三个属性：
 * flex-grow表示是否会自动增大，1为是，0为否，默认1
 * flex-shrink表示是否自动缩小，1为是，0为否，默认1
 * flex-basis：宽度px/百分比/数字

因此，如果要在flex元素上使用定宽操作，那就必须要按照下面的代码去添加
```css
el {
  flex:0 0 90px /*要设置的宽度*/
}
```

同时记得给容器元素加上允许换行的属性：
```css
 parent {
   flex-wrap:wrap;
 }
```
不然元素总宽度超出父容器的时候，默认是不换行的。
