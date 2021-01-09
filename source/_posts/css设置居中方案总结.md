---
title: css设置居中方案总结
date: 2018-04-02 09:25:44
tags:
  - css
categories:
  - css
---


　　回想一下，自己平时项目里遇到的比较多的就是css如何让元素居中显示，其实差不多每种情况都遇到过，所采用的方法也都各有利弊，下面对这些方法来做个概括，对其中的坑点，也会一一指出来，希望能给遇到问题的同学一些参考： 

## 一、水平居中
###  01 行内元素 text-align: center;
```css
.parent {
   text-align: center;
}
```
### 02 块级元素 margin: auto;
**注意：低版本的浏览器还需要设置text-align:center;**
```css
.parent {
    text-align: center; 
}
.child {
    margin: auto; 
    border: 1px solid blue;
}
```
## 二、垂直居中
### 01 行内元素（单行文字垂直居中）：设置 line-height = height；
```css
.parent {
   height: 200px;
   line-height: 200px;
   border: 1px solid red;
}
```
### 02 块级元素：绝对定位（需要提前知道尺寸）
优点：兼容性不错  

缺点：需要提前知道尺寸，可拓展性和自适应性不好  
```css
.parent {
    position: relative;
    height: 200px;
}
.child {
    width: 80px;
    height: 40px;
    background: blue;
    position: absolute;
    left: 50%;
    top: 50%;
    margin-top: -20px;
    margin-left: -40px;
}
```
### 03 块级元素：绝对定位 + transform
优点：不需要提前知道尺寸  
缺点：兼容性不好，只支持ie9+浏览器，而且还会引发一些其他的奇怪的兼容问题  
```css
.parent {
    position: relative;
    height: 200px;
}
.child {
    width: 80px;
    height: 40px;
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    background: blue;
}
```
### 04 块级元素：绝对定位 + margin: auto;
优点：不需要提前知道尺寸，兼容性好  
缺点：我目前还没有遇到。  
此方法出自张鑫旭老师的博客，我也是了解了之后，才运用到实际开发中的  
```css
.parent {
    position: relative;
    height: 200px;
}
.child {
    width: 80px;
    height: 40px;
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    margin: auto;
    background: blue;
}
```
### 05 块级元素：padding
缺点：如果高度固定，需要提前计算尺寸（**只在某些特定情况适用**）
```css
.parent {
    padding: 5% 0;
}
.child {
    padding: 10% 0;
    background: blue;
}
```
### 06 块级元素：display: table-cell
**父元素一定要设置display:table，这样子元素的table-cell才能生效**
```css
.parent {
    width: 600px;
    height: 200px;
    border: 1px solid red;
    display: table;
}
.child {
    display: table-cell;
    vertical-align: middle;
}
```
### 07 块级元素：display: flex（移动端页面推荐）
缺点：兼容性不好
```css
.parent {
    width: 600px;
    height: 200px;
    border: 1px solid red;
    display: flex;
    align-items: center;
    justify-content: center;  /*水平居中*/
}
.child {
    background: blue;
}
```
### 08 块级元素：伪元素
这个方案是从张鑫旭大神的新书：《css世界》里读到的

vertical-align这个属性需要与同元素内的行内元素的基线为参考，高度100%自然就以高度50%处即平常所说的中线为基线，middle默认对齐其基线，自然也就垂直居中对齐了
```css
.parent {
    width: 300px;
    height: 300px;
    border: 1px solid red;
    text-align: center;
}
.child {
    background: blue;
    width: 100px;
    height: 40px;
    display: inline-block;
    vertical-align: middle;
}
.parent::before {
    content: '';
    height: 100%;
    display: inline-block;
    vertical-align: middle;            
}
```
### 09 块级元素：inline-block
HTML代码：
```html
<div class="parent">
    <div class="child">child</div>
    <div class="brother">brother</div>
</div>
```
CSS代码：
```css
.parent {
    width: 400px;
    height: 400px;
    border: 1px solid red;
    position: relative;
}
.child, .brother {
    display: inline-block;
    vertical-align: middle;
}
.child {
    background: blue;
    font-size: 12px;
}
.brother {
    height: 400px;
    font-size: 0;
}
```
### 其他
table布局是十年前前端最常用的布局方式，最省心但是冗余也很多，结构嵌套也比较深。现在前端变化日新月异，不推荐使用

```html
<table>
     <tr>
         <td align="center" valign="middle">content</td> 
     </tr>
</table>
```


结语：希望上述的总结对相关的同学能起到参考性的作用。