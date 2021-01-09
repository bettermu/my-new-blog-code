---
title: 记一次百度地图容器父级position定位产生的bug
date: 2020-01-11 19:40:55
tags:
  - 百度地图
  - css
categories:
  - 百度地图
  - css
---



> 最近在做一个地图选点的功能，业务逻辑是这样的：页面上有按钮，点击弹出弹窗，弹窗里有地图，可以在地图上选点，然后获取经纬度，赋值给页面中的Input框。页面截图如下：

![](https://github.com/bettermu/blog-picture-store/blob/master/20200111/1.png?raw=true)   

![](https://github.com/bettermu/blog-picture-store/blob/master/20200111/2.png?raw=true)   

### 简介

> 我们来分析下，主要的业务流程：点击定位按钮，初始化地图，然后就是用户选点，确定按钮，更改经纬度的值，这里js逻辑很简单，不做过多阐述。

> 一般这样的页面，我们很轻松可以写出如下的元素结构：
```html
<div class="pop-wrap">
    <div class="pop">
        <div class="map-container"></div>
    </div>
</div>
```
> 我们的弹窗布局是典型的完全居中式布局，那么这又是个经典的布局模式。完全居中的方式有很多种：

1. 使用绝对定位布局
> 使用绝对定位布局，那么就意味着脱离的文档流：

```css
.pop-wrap {
    position:fixed;
    top:0;
    left:0;
    right:0;
    bottom:0;
}
.pop {
    height:600px;
    width:800px;  
    position:absolute;
    top:50%;
    left:50%;
    
    //方式1 这里的负值margin，我们需要显示设置宽高，并且为宽高值的一半
    margin-left:-400px;
    margin-top:-300px;  
    
    
    //方式2 不需要设置宽高，开启渲染加速
    transform:translate3d(-50%,-50%,0) 
    
    //方式3  也需要设置宽高 
    margin:auto;
    top:0;
    left:0;
    bottom:0;
    right:0;
}
```
2. flex布局

> 使用flex就比较简单了  属性设在父级就可以了  而且子元素不用设置宽高：

```css
.pop-wrap {
    position:fixed;
    top:0;
    left:0;
    right:0;
    bottom:0;
    display:flex;
    justify-content:center;
    align-items:center
}
.pop {
    
}
```
3. table布局

> table布局也可以达到，但是一般不建议使用：

```css
.pop-wrap {
    position:fixed;
    top:0;
    left:0;
    right:0;
    bottom:0;
    display:table;
}
.pop {
    display:table-cell
}
```


### 出问题的地方
> 那么实际，我使用的是第一种布局，也就是position absolute + 负margin的，这种方式兼容性要稍微好一些，但是在地图加载之后，测试出的问题，有下面这两个：
* 窗口弹出之后，可以拖动，但是经常出现无法正常缩放
* 缩放可以缩放，但是缩放中心点偏移很多

### 解决方式

1. 无法缩放的解决

> 根据以前遇到关于百度地图position相关的问题，缩放不正常这个问题，一般是因为容器定位或者父级定位的问题，关键就在于，容器父级有脱离文档流的定位的时候，如上面的.pop的div，地图的缩放就会出现问题，但这种问题，不是必然出现。

> 仔细询问了产品，项目兼容的范围，是否可以换用flex，得到肯定的答复之后，我决定将.pop的position给正常化，改成flex的方式去完成居中的布局。

2. 缩放偏移的解决

> 经过排查，我发现，只要页面出现滚动条，那么滚轮在地图上操作，就会出现缩放中心点偏移的奇怪现象，那么也很简单，当弹窗弹出的时候，将body overflow hidden掉，关闭弹窗再复原，就可以解决。




