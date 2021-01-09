---
title: img 标签 访问图片 返回403 forbidden问题
date: 2018-03-06 10:00:11
tags:
  - 静态资源
categories:
  - img
---
之前在项目里，本地调试的时候，图片src引用了第三方网站的图片资源，导致控制台出现了如下的报错：
![](https://github.com/bettermu/blog-picture-store/blob/master/20180306/1.png?raw=true)

403 forbidden，说明了这个网络资源这样获取是被拒绝的，那么通过简单的百度，找到了相关的解决方法，并去实际尝试：

其实呢，解决这个问题只需要在头部添加一个meta   
```html
<meta name="referrer" content="no-referrer" />
```
关于meta referrer，有如下可设置的值：  
![](https://github.com/bettermu/blog-picture-store/blob/master/20180306/2.png?raw=true)

经过实际测试，该方法确实有效，能够解决这个问题。