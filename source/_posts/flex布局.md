---
title: flex布局
date: 2018-05-29 21:48:03
tags: 
  - css
  - flex
categories: 
  - css
---

## 容器属性

* flex-direction
* flex-wrap 
* flex-flow
* justify-content
* align-items
* align-content



### flex-direction 
决定主轴的方向，即项目的排列方向  
* row(默认值)：主轴为水平方向，起点在左端 。 
* row-reverse：主轴为水平方向，起点在右端。
* column：主轴为垂直方向，起点在上端。
* column-reverse：主轴为垂直方向，起点在下端。


### flex-wrap
定义子元素的换行方式  
* nowrap(默认)：不换行。
* wrap：换行，新的一行在之前一行的下方。
* wrap-reverse：换行，新的一行在之前一行的上方。

### flex-flow
flex-direction和flex-wrap的简写形式 默认值为 row nowrap

### justify-content
定义了项目在主轴上的对齐方式。
* flex-start(默认值)；左对齐
* flex-end：右对齐
* center：居中
* space-between：两端对齐，项目之间间隔相等。
* space-around：每个项目两侧的间隔相等，项目之间的间隔比项目边框的间隔大一倍。

### align-items
定义项目在交叉轴（即与主轴垂直的轴）上如何对齐
* flex-start：交叉轴的起点对齐
* flex-end：交叉轴的终点对齐
* center：交叉轴的中点对齐
* baseline：项目的第一行文字的基线对齐
* stretch（默认值）：如果未设置高度或者高度为auto，将占满整个容器的高度。

### align-content
定义了多根轴线的对齐方式，如果项目只有一根轴线，则该属性不起作用

* flex-start：与交叉轴的起点对齐。
* flex-end：与交叉轴的终点对齐。
* center：与交叉轴的中点对齐。
* space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
* space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
* stretch（默认值）：轴线占满整个交叉轴。

## 项目的属性

* order
* flex-grow
* flex-shrink
* flex-basis
* flex
* align-self

### order
定义项目的排列顺序，数值越小，排列越靠前，默认为0

### flex-grow
定义项目的放大比例，默认为0

### flex-shrink
定义了项目的缩小比例，默认为1

### flex-basis
定义了在分配多余空间之前，项目占据的主轴空间，浏览器根据这个属性，计算主轴是否还有多余空间，默认值为auto，即项目本来的大小。可以设置跟width 和height一样的值，项目占据固定空间

### flex
是flex-grow flex-shrink flex-basis的简写  默认为0 1 auto  建议优先使用

### align-self
允许单个项目有与其他项目不一样的对齐方式，可以覆盖容器的align-items属性，默认值为auto，表示继承父元素的align-item属性，如果没有父元素，则等同于stretch


