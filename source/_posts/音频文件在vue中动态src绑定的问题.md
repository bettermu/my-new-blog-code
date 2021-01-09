---
title: 音频文件在vue中动态src绑定的问题
date: 2018-10-24 22:06:19
tags:
  - vue 
  - h5 audio
categories:
  - vue 
  - h5 audio
---


就在今天晚上，刚刚，两个多月前开发的黄山学院智慧旅游助手app项目，在临上线交付的前2天，里边语音导览出现了一个比较严重的问题，而且是在特定机型下才出现，且听我说。

问题大概描述是这样的，首先进入语音导览页面，底图是百度地图，地图上面展示各种景点的图标，如下图所示，带有喇叭图标的，就是带音频的，不带喇叭的，就是简单文字展示的：  

![](https://github.com/bettermu/blog-picture-store/blob/master/20181022/1.png?raw=true)
![](https://github.com/bettermu/blog-picture-store/blob/master/20181022/2.png?raw=true)

需求是点击图标或者底下的横栏扩音器按钮，是可以播放当前景点音频的。但实际发现的问题，在android5.1系统（需求要求的最低版本是5.1）下，刚进来，第一次点击图标，无语音播放，再点第二次，才播放当前景点的语音，但是，当我切换一个图标点击的时候，播放的，还是之前点击的景点图标的语音，点第二次，才能播放当前景点的语音。总之问题的现象就是，实际音频的加载和播放，总是比交互要慢半拍。

核心代码如下，先看html：
```html
<audio controls id="voicemp" class="dn">
    <source :src="voiceUrl" type="audio/mpeg">
</audio>
```

```js
  var app = new Vue({
    el:'#app',
    data:{
      voiceUrl:'',
    }
  })

  //异步获取到音频文件之后...
  asyncFun(function(url){
    app.voiceUrl=url
    //然后就是调用播放方法，
    
    //切换时清空地址、
    app.voiceUrl=''

  })
```

上面方法调用，可以看出来，音频加载是异步动态绑定的，也就是说，等数据加载好之后，音频才能动态绑定上去，众所周知，异步的事件，总是会不太靠谱，很多时候，因为各种环境因素影响，会导致结果的多样化呈现，初步估计，是由于音频加载需要时间，但是可能vue内部的虚拟dom实现，挂载的速度要快于音频加载完成的时间，因此，当点击事件生效了，音频仍然没有加载完成，就可能导致播放失败，继而导致后续的播放队列产生紊乱。

简单理清楚之后，解决办法很简单，不要使用vue的:src绑定，而是使用h5audio标签原有的属性src，写法有两种，一种是给audio加上ref属性，通过refs获取dom元素，再去动态设置src属性，另外一个就是采用js元素的获取dom元素的方法：

```html
<audio controls id="voicemp" class="dn">
    <source src="" type="audio/mpeg">
</audio>
```

```js
  var app = new Vue({
    el:'#app',
    data:{
      voiceUrl:'',
    }
  })

  //异步获取到音频文件之后...
  asyncFun(function(url){
    //app.voiceUrl=url
    document.getElementById("voicemp").src=url;
    //然后就是调用播放方法，
    
    //切换时清空地址、
    //app.voiceUrl=''
    document.getElementById("voicemp").src=''

  })
```
通过这样的方式去设置src，避免了vue生命周期对于音频文件的干扰。

ps：其实自己写过的一个音乐app的demo，audio标签很好的和:src使用，其实用的是vuex的状态管理方式，其实，音频文件并不算异步的，因为url早就是父组件中通过异步获取然后存入state中，因此，在子组件里使用:src去绑定一个值，其实相当去同步的方式去获取，就不会出现这样的一种异步导致数据混乱的状态了，详细请看：[这里](https://github.com/bettermu/vue-music-app/blob/master/src/components/player/player.vue)

一般平时很少会遇到这样稀奇古怪的错，而且是在特定的机型下面，这个例子，在网页端和android6以上以及ios系统，都是完全正常的，就只有5.X的版本才会有。大概遇到了，真的只能一点一点去摸索，才能发现。踩过坑了，以后也就会多注意下。



