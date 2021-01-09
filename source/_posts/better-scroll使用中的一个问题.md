---
title: better-scroll使用中的一个问题
date: 2018-08-14 21:08:13
tags:
  - better-scroll
  - js
categories:
  - js
---


better-scroll是移动端页面做列表滑动效果的常用插件，本人自从第一次使用，就对它特别喜欢，之前在自己写的网易云音乐APP里，有使用过，这次，更是拿到了项目里，进行滚动交互优化的使用，常用在一些展示列表里，下拉刷新，上拉加载。效果真的挺好，使用过程中，也遇到过一些问题：

* 比方说，如果scroll的wrapper下面，包含不止一个子元素，滚动就会失效，就像下面这样的结构：

```html
<div class="scroll-wrapper">
  <div class="tip">上拉刷新提示</div>
  <ul>
  .......<!--真正的滚动列表-->
  </ul>
</div>
```

上面的结构里，初始化容器内的直接子元素，超过了1个，因此，初始化是失效的，必须让容器元素有且仅有一个直接子元素,像下面这样改造一下：

```html
<div class="scroll-wrapper">
  <div class="inner-wrapper">
    <div class="tip">上拉刷新提示</div>
    <ul>
    .......<!--真正的滚动列表-->
    </ul>
  </div>  
</div>
```

* 比方说，一般来说，我们在写列表的时候，父容器一般是不设高度的，内容完全由子元素撑开，但是对于better-scroll的初始化容器来说，不设高度，滚动的效果也会因此失效，所以我们需要给容器加个固定高度，并且最重要的是，要加上overflow:hidden的属性：

```css
html,body {
  height:100%;
}
.scroll-wrapper {
  height:100%;
  overflow:hidden;/*此属性非常重要，直接影响滚动是否生效*/
}
```


但是呢，今天要讲的，并不是这些问题，而是另外一个，应该算是问题的问题...

看了better-scroll文档，知道其内部是有整合刷新和加载相关的api的，通过正常的option配置，以及监听所对应的特定的触发事件，就可以达到很好的效果，具体的配置这里不提，可自行查看文档，但是，这个内部实现加载和刷新机制的插件，有个问题，这也是本人在实际项目里所发现的，移动端页面，在监听了插件自己内部实现的刷新机制后，用户向下滑动列表，滑动一段距离后，再突然往上滑动列表，列表会与容器顶部，产生很大的一段距离，并且回弹不回去了，相关配置代码如下：

```js
var scroll = {
    scrollObj: null,
    init: function () {
      this.scrollObj = new BScroll(doc.getElementById("scroll"), {
        pullUpLoad: {
          threshold: -10
        },
        preventDefault: false,
        probeType: 1,
      })

      this.scrollObj.on("pullingUp",function(){
        //上拉加载的操作
      })
      
    }
  }
```

上面代码，使用的是监听插件封装的pullingUp事件，也就是触底加载的事件，但是会出现，连续滑动，回弹不回去的画面，很影响用户交互，因此，不能使用插件自带的pullingUp监听，那么，如果不使用插件自带的监听，只能自己实现上拉加载和下拉刷新了

好在better-scroll是强大的，其内部提供出来的一些属性和API，足够我们自己做一个独立的滚动加载封装了，整理后的代码如下：

```js
var scroll = {
    scrollObj: null,
    init: function () {
      this.scrollObj = new BScroll(doc.getElementById("scroll"), {
        probeType: 1,
        scrollY: true,
        click: true,
      })
      
      this.scrollObj.on("touchEnd", function (pos) {
        
        if (pos.y > 30) {

          //下拉刷新的代码...

        } else if (pos.y < (this.maxScrollY - 30)) {
          
          //上拉加载的代码...
          
          this.finishPullUp() //这是必须要进行的一步，告诉插件，下拉已完成，不必再触发
        }
      })

    }
  }
```

我们监听实例的touchEnd事件，事件结束的每次，都能获取到scroll实例对象的纵向移动距离，根据移动距离判断，来进行相应的刷新和加载操作。这样，通过自己封装的逻辑，在真机测试的页面里，不论怎么连续滑动，页面容器都不会出现很大的滑动距离。
