---
title: 关于js中 toFixed()的一个小坑
date: 2018-05-16 10:33:37
tags:
  - js基础
categories:
  - js
---

作为一名前端，大家都应该知道，toFixed()的作用，toFixed()经常用于前台与后台数据格式的转换，套用下w3c上面的定义：　　　

## 定义和用法
toFixed(n) 方法可把 Number 四舍五入为指定小数位数的数字。n为保留的小数位数
## 返回值
```javascript
  typeof (1.0.toFixed())   //string
```



返回值是string类型的

其实大部分的时候，我们在项目中运用，都是将多位的小数，转化成固定位数的小数，比如

```javascript
  1.000000.toFixed(2)   //"1.00"
```

但是有的时候 我们需要将整数值转换成小数，如

```javascript
  1.toFixed(2)  //你们知道这个结果是怎么样的吗
```
这个问题，是我在一次项目中遇到的，当时也没花太多时间，要想解决这个问题，就要深刻理解js引擎的编译原理和过程，已经相关类型值的理解。

其实是这样的，上面的整数化成小数，存在一个隐藏的坑，就是如果直接用整数直接调用toFixed(),比如1.toFixed(),那么讲会出现如下的报错：
![](https://github.com/bettermu/blog-picture-store/blob/master/20180516/toFixed%E9%97%AE%E9%A2%98.png?raw=true)

原因就是，js引擎在运行的时候，默认将1后面的那个点，认为是小数点，所以1.toFixed()也就相当于 1.0toFixed()，当然报错啊。

其实想到的转化方法有很多：

* 1..toFixed() 这个方法最直接，不是少了一个点么 那就直接在后面添加一个点，这样就相当于1.0.toFixed()  
![](https://github.com/bettermu/blog-picture-store/blob/master/20180516/1.png?raw=true)

* 可以将1存成一个变量名，用变量调用toFixed() 结果也是想要的  
![](https://github.com/bettermu/blog-picture-store/blob/master/20180516/2.png?raw=true)

* 由上面的解决方法的引申，可以有另外一个方法，就是用一对()将1和后面的点隔离，那么js引擎在运行的时候，就会将点变成是1这个整体对于toFixed()方法的调用  
![](https://github.com/bettermu/blog-picture-store/blob/master/20180516/3.png?raw=true)


结语：条条大路通罗马，没有解决不了的问题，将所遇到的小知识点分享出来，与大家共勉。
