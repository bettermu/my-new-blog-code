---
title: Android Studio布局加载器不显示的问题
date: 2018-06-22 21:01:00
tags:
  - Android Studio
categories:
  - Android
---

最近闲来无事，在倒腾安卓开发，在配置好所有的环境，以及安装了Android Studio的IDE后，首先尝鲜的第一个例子，就是在界面上绘制一个Button组件，明明配置好了按钮组件，但是，在Android Studio自带的布局加载器里，却不显示，如下图：  

![](https://github.com/bettermu/blog-picture-store/blob/master/20180622/2.png?raw=true)

但是当我在虚拟机中运行这个项目的时候，却又奇迹般的渲染上去了：   

![](https://github.com/bettermu/blog-picture-store/blob/master/20180622/1.png?raw=true)

这到底是什么原因呢，作为一个喜欢折腾，又想把事情弄清楚的前端小工，我还是选择了网上搜索答案，那么网上给的答案真的是五花八门，什么环境问题啊 版本问题啊，之类的，我都逐一排除了，后来我最终找到了可以一试的方法：  

将style文件中的theme改一下，在Theme.AppCompat.Light.DarkActionBar前面加上Base.  如下    

![](https://github.com/bettermu/blog-picture-store/blob/master/20180622/3.png?raw=true)

改变成下面的样子：  

![](https://github.com/bettermu/blog-picture-store/blob/master/20180622/4.png?raw=true)

这回再进布局加载器，果然，一切就正常了，添加的Button组件也正常展示了：

![](https://github.com/bettermu/blog-picture-store/blob/master/20180622/5.png?raw=true)