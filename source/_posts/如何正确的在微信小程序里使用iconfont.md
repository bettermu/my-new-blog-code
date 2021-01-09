---
title: 如何正确的在微信小程序里使用iconfont
date: 2018-10-21 22:07:22
tags:
  - 微信小程序 iconfont
categories:
  - 微信小程序
  - css3字体
---
---

iconfont给前端带来的便利相信已众所周知，我也一直遵循能用iconfont的情况下，绝不用image。但是这周开始接触小程序，却发现小程序里不能按照之前的方式愉快的使用了。

## 此前

使用打开[iconfont官网](http://www.iconfont.cn/)，选择需要的icon，加入到个人项目里。  

![](https://github.com/bettermu/blog-picture-store/blob/master/20181020/1.jpg?raw=true)

然后选择Unicode模式，点击复制代码，将在线资源复制的全局CSS文件里。再用一个专用class(.iconfont)用来定义iconfont。  

```css
@font-face {
  font-family: 'iconfont';  /* project id 319212 */
  src: url('//at.alicdn.com/t/font_tdeh59rfkwdhd7vi.eot');
  src: url('//at.alicdn.com/t/font_tdeh59rfkwdhd7vi.eot?#iefix') format('embedded-opentype'),
  url('//at.alicdn.com/t/font_tdeh59rfkwdhd7vi.woff') format('woff'),
  url('//at.alicdn.com/t/font_tdeh59rfkwdhd7vi.ttf') format('truetype'),
  url('//at.alicdn.com/t/font_tdeh59rfkwdhd7vi.svg#iconfont') format('svg');
}

.iconfont {
  font-family: "iconfont";
  font-size: .16rem;
  font-style: normal;
  color: #9e9595;
}
```

接着鼠标hover到图标上点击复制代码，然后在html里面用一个标签包裹即可愉快的使用了。

```html
<i class="iconfont">&#xe604;</i>
```

![](https://github.com/bettermu/blog-picture-store/blob/master/20181020/2.jpg?raw=true)

## 但是

在小程序里面，按照这种方式继续用的话，就。。。尴尬了😅

```html
<text class="iconfont">&#xe604;</text>
```

![](https://github.com/bettermu/blog-picture-store/blob/master/20181020/3.jpg?raw=true)

漂亮的图标，变成了让人心慌的代码。这可怎么办？？？   
    
很多网友推荐将字体图标转化成base64😅😅。虽然这也不乏是一种解决方案，但是，显得很麻烦。特别是在项目初期，不确定需要用哪些图标的时候，加一个图标转一次，特别心累。



## 美好的事情即将发生

经过一番研究后，我发现还是可以按照以前的方式引用在线资源。不过在html里面使用的时候我们需要用到伪类元素。说到这个大家应该都有底了吧，哈哈。   


回到我们第一张图:   

![](https://github.com/bettermu/blog-picture-store/blob/master/20181020/1.jpg?raw=true)   

我们需要在原来的CSS基础上，再为每一个图标自定义一个class名。比如这开灯的icon:

```css
.icon-kaideng:before { 
  content: "\e604";
  font-size: 36rpx;
  color: #5FB7EB;
}
```

content里面的内容为字体编码的后4位加一个\，  

接着，在wxml里面这么使用就好了

```html
<icon class="iconfont icon-kaideng"/>
```

这个问题就顺利解决了。


## 原理

由于wxml不支持iconfont字符串渲染，因此才会出现这样的问题，但是css伪类还是支持的。所以，另辟蹊径，用伪类解决展示图标，是个好办法。


