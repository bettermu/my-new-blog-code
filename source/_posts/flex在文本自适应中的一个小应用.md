---
title: flex在文本自适应中的一个小应用
date: 2018-10-28 20:12:37
tags:
  - css
  - flex
categories:
  - css
  - flex
---

最近在项目里遇到了一个问题，如图:

![](https://github.com/bettermu/blog-picture-store/blob/master/20181023/1.png?raw=true)

## 结构和样式代码

```html
<div class="clearfix luggage-type-w">
  <!-- 文字块 -->
    <div class="fl ell luggage-type luggage-type-tools"></div>
    <!-- 虚线块 -->
    <div class="rel link-line lt-link-line"></div>
</div>
```

```css

.fl {
  float:left;
}

.rel {
  position:relative;
}

.luggage-type {
    padding-left: 25px;
}

.luggage-type-tools {
    padding-top: 2px;
    background: url("../../img/detail/yp-icon.png") left center no-repeat;
    color: #f98d4b;
}
.link-line {
    margin: 0 5px;
    border-bottom: dashed 2px #dcdcdc;
}
.lt-link-line {
    top: 8px;
    margin: 0 12px 0 70px;
}
```

## 起因

其实问题很明显，左边的文字不定宽，已经浮动脱离文档流，但是右边的虚线，是相对自己定位，往下移动，底部虚线边框，但是布局是写死的，由样式表文件能看出来，margin属性是定死的，这样就导致，当文字超过目前的两个字时，会被虚线直接穿过，这样就导致了布局的错乱，而需求是要文字不能定死的，自适应。因此需要作出调整。

## 处理方式1

前端同事是这么做的，不就是文字没定宽，导致了虚线穿透么，那很好办啊，直接加上文字截断就好了，定个最大宽度：

```css
.ell {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}

.luggage-type-tools {
    padding-top: 2px;
    max-width: 65px;
    background: url("../../img/detail/yp-icon.png") left center no-repeat;
    color: #f98d4b;
}
```

效果如下：  

![](https://github.com/bettermu/blog-picture-store/blob/master/20181023/2.png?raw=true)  

## 问题

其实能看出来，定宽截断的效果确实能够阻挡虚线穿透，但是，显得太过生硬，一方面，截断掉文字，对用户体验非常不友好，毕竟截断的应用场景，仅仅是超出当前文本行的处理，在这里，显得太不适合。另一方面，给个定宽，如果没有达到宽度怎么办，那就像上图一样，右边会留出空白，我想，作为一个合格的前端工程师，是不会容忍这样的瑕疵出现的，因此，问题还是没有解决，毕竟没有通过PM的眼睛啊，于是我尝试着给出了自己的处理方式：

## 处理方式2

其实简单分析下，这样的需求，从布局上面，就算是伸缩自适应的弹性布局，那么对于这么一个外部容器来说，其内部就是弹性的，那么可以考虑使用flex进行处理(项目无需兼容ie9及以下的浏览器，因此是可以兼容使用flex的)

先给出我的样式代码吧：

```css
.luggage-type-w {
    display: -webkit-box;
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
}

.luggage-type-tools {
    padding-top: 2px;
    /* max-width: 65px; */
    background: url("../../img/detail/yp-icon.png") left center no-repeat;
    color: #f98d4b;
}

.lt-link-line {
    top: -8px;
    /* margin: 0 12px 0 90px; */
    -webkit-box-flex: 1;
    -webkit-flex: 1;
    -ms-flex: 1;
    flex: 1;
}

```

效果图如下：   

![](https://github.com/bettermu/blog-picture-store/blob/master/20181023/3.png?raw=true)  

## 原理：  

flex有个属性，就是容器内部元素flex:1，其实是下面这种写法的缩写：

```css
ele {
  flex:1 1 auto;
}
```

第一个1代表flex-grow，是指子元素占据的父元素空间，默认值为0，所有子元素的flex-grow数值加在一起，父元素剩下的空间按照比例分配，第二个1是flex-shrink，用来定义伸缩项目收缩的能力，默认值是1,0的时候不改变，第三个就是flex-basis,通俗点，就是该元素占据的宽度,默认值为auto。  

实际上，不加上flex属性，其实flex的属性就是默认的 0 1 auto，因此，当子元素没有flex属性的时候，flex-grow为0，不参与弹性计算。那给虚线块加上flex-grow:1的属性，也就是让他占据剩下的空间，达到自适应的目的。

其实在这里使用的方式很简单，给父容器定义flex的盒模型，对于其里面的子元素，因为要求是虚线根据文字块的宽度动态自适应布局，因此，这里的文字块div无需给出相关flex属性，而在虚线块上面添加flex:1，是因为flex:1其实是设置flex:1的子元素平均分配父元素的所有空间，（不包括没设置flex属性的子元素，没设置flex的子元素，默认是flex-grow:0不参与弹性计算）,那其实意思就是，案例里，在父元素容器中，虚线占据了除去文字以外的剩余空间，那么无论文字有多少，虚线都会根据文字的长度，自适应的占据剩余空间。

## 总结

其实flex现在真的是基本算是可以随意使用了，至少我是在移动端页面可以毫无顾忌的使用，在pc端，只要不考虑兼容鬼畜的IE，基本按照兼容模式写写，都是能够OK的，毕竟flex实在太强大，很多以前要用奇技淫巧才能完成的样式，flex简单几句就能够强大的完成。

很多时候，重要的不仅仅是问题解决的结果，更重要的是解决问题的思路，以及总结。



