---
title: css相关技巧总结(一)
date: 2018-06-16 22:28:39
tags:
  - css
categories:
  - css
---


相信大家在日常的开发工作中，在样式布局上面，会遇到一些问题，而对于这些问题的解决呢，网上的相关答案有比较凌乱，不好去整理，下面我将总结一下自己在项目里，所遇到的那些关于CSS样式问题的解决方案。

## 伸展一个元素到窗口高度
在具体场景中，你可能想要将一个元素伸展到窗口高度，基本元素的调整只能调整容器的大小，因为元素的高度是可继承的，元素的高度100%的实现，顶层的body元素和html元素必须有100%的高度属性，因此，要使一个元素伸展到窗口高度，我们需要伸展顶层元素：html和body:
```css
html, 
body {
    height: 100%;
}
```

然后将100%应用到任何元素的高

```css
div {
    height: 100%;
}
```

## 用CSS动画实现省略号动画
其实这个经常用在加载效果上面，很多时候，加载效果是显示一张gif图片，但是图片比较占用性能和资源，所以，还是纯文字的比较好，那么如何去实现这样一种效果呢，也就是在元素后面，添加一个伪元素，伪元素的宽度，是不断的循环变化的，如下代码所示：

```css
.loading:after {
    overflow: hidden;
    display: inline-block;
    vertical-align: bottom;
    animation: ellipsis 2s infinite;
    content: "\2026"; /* ascii code for the ellipsis character */
}
@keyframes ellipsis {
    from {
        width: 2px;
    }
    to {
        width: 15px;
    }
}
```
infinite代表无限循环的意思，也就是after伪类的宽度是一直在变化的，也就实现了动画的效果。

## 样式重置
关于css样式重置，每个公司应该都有自己的一套规范，样式重置与标准化是两个不同的概念，重置则是完全抛弃浏览器的默认样式，而标准化则是按照实际需要去优化浏览器默认的样式，如业界常用的normalize.css如下的代码就是经典的样式重置：
```css
html, body, div, span, applet, object, iframe, h1, h2, h3, h4, h5, h6, p, blockquote, pre, a, abbr, acronym, address, big, cite, code, del, dfn, em, img, ins, kbd, q, s, samp, small, strike, strong, sub, sup, tt, var, b, u, i, center, dl, dt, dd, ol, ul, li, fieldset, form, label, legend, table, caption, tbody, tfoot, thead, tr, th, td, article, aside, canvas, details, embed, figure, figcaption, footer, header, hgroup, menu, nav, output, ruby, section, summary, time, mark, audio, video {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
  outline: none;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
}
html { height: 101%; }
body { font-size: 62.5%; line-height: 1; font-family: Arial, Tahoma, sans-serif; }
article, aside, details, figcaption, figure, footer, header, hgroup, menu, nav, section { display: block; }
ol, ul { list-style: none; }
blockquote, q { quotes: none; }
blockquote:before, blockquote:after, q:before, q:after { content: ''; content: none; }
strong { font-weight: bold; } 
table { border-collapse: collapse; border-spacing: 0; }
img { border: 0; max-width: 100%; }
p { font-size: 1.2em; line-height: 1.0em; color: #333; }
```

## 跨浏览器的透明方案
透明元素的使用，在现在是越来越频繁了，那么面对主流的浏览器对于透明元素的定义的不同，如下的代码则是统一了浏览器的透明效果：
```css
.transparent {
    filter: alpha(opacity=50); /* internet explorer */
    -khtml-opacity: 0.5;      /* khtml, old safari */
    -moz-opacity: 0.5;       /* mozilla, netscape */
    opacity: 0.5;           /* fx, safari, opera */
}
```

## 强制出现滚动条
大多数的情况，关于浏览器滚动条，我们都是定义自动滚动的，也就是说，只在内容超出窗口高度的时候，才让它出现滚动条，那么在有些需求下，滚动条必须要出现，那该怎么办呢？
```css
html { height: 101% }
```
让根元素的高度超出窗口高度，就可以强制出现滚动条了。

## 在可点击的项目上强制手型
设置手型，是比较常用的属性，但是基本上我们在使用到的时候，才会去局部添加，这样的话样式会有点冗余，解决方法就是设置一个公用的手型属性：
```css
a[href], input[type='submit'], input[type='image'], label[for], select, button, .pointer {
    cursor: pointer;
}
```


## 禁用移动Webkit的选择高亮

```css
body {
    -webkit-touch-callout: none;
    -webkit-user-select: none;
    -khtml-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
```
