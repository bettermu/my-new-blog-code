---
title: 记一次项目里关于el-scrollbar的使用
date: 2019-11-12 21:40:57
tags:
  - elementUI
  - scroll
categories:
  - elementUI
  - scroll
---


## 记一次项目里关于el-scrollbar组件的使用

### 由来

> 年初开发的泾县的项目，其中包含了触控一体机的四个系统，其实就是使用chrome浏览器进行包裹的外壳，里面就是纯web的页面。这几个系统，这几天在上线，线上发现了这样的一个奇怪的问题，就是项目中，因为优化用户使用体验，对于列表的滚动，统一使用了基于Vue的一个组件，better-scroll。而这个组件，在一体机的环境里，无法滚动，意思就是说，手指操作无法进行列表的滚动。

> 其实better-scroll组件，在移动端使用的较多，但是后面更新的版本，支持了PC端的鼠标操作，因此在PC端，也可以使用这个插件，来代替浏览器本身的滚动以及丑陋的滚动条了。这些功能其实当时开发的时候，使用浏览器，是完全可以操作，没有任何问题的。也是第一次在一体机环境里使用better-scroll这种东西，当时心里面有预案，万一将来上生产了，出问题怎么办，要有planB。

### 目前使用的方式

> 其实项目里的scroll插件，已经经过了一次简单的封装，能够复用：

```
<template>
  <div class="wrapper" ref="wrapper">
    <div class="rel wrap-inner">
      <slot></slot>
      <div class="tc f30 c999 bottom-tip">{{bottomTip}}</div>
    </div>
  </div>
</template>

<script>
import BScroll from "better-scroll";

export default {
  name: "scroll",
  props: {
    /**
     * 1 滚动的时候会派发scroll事件，会截流。
     * 2 滚动的时候实时派发scroll事件，不会截流。
     * 3 除了实时派发scroll事件，在swipe的情况下仍然能实时派发scroll事件
     */
    probeType: {
      type: Number,
      default: 1
    },
    /**
     * 点击列表是否派发click事件
     */
    click: {
      type: Boolean,
      default: true
    },
    /**
     * 是否开启横向滚动
     */
    scrollX: {
      type: Boolean,
      default: false
    },
    /**
     * 是否派发滚动事件
     */
    listenScroll: {
      type: Boolean,
      default: true
    },
    /**
     * 列表的数据
     */
    data: {
      type: Array,
      default: null
    },
    /**
     * 是否派发滚动到底部的事件，用于上拉加载
     */
    pullup: {
      type: Boolean,
      default: true
    },
    /**
     * 是否派发顶部下拉的事件，用于下拉刷新
     */
    pulldown: {
      type: Boolean,
      default: true
    },
    /**
     * 是否派发列表滚动开始的事件
     */
    beforeScroll: {
      type: Boolean,
      default: false
    },
    /**
     * 当数据更新后，刷新scroll的延时。
     */
    refreshDelay: {
      type: Number,
      default: 20
    }
  },
  data() {
    return {
      topTip: "下拉刷新",
      bottomTip: "",
      noMoreData: false
    };
  },
  mounted() {
    // 保证在DOM渲染完毕后初始化better-scroll
    setTimeout(() => {
      this._initScroll();
    }, 20);
  },
  methods: {
    _initScroll() {
      if (!this.$refs.wrapper) {
        return;
      }
      // better-scroll的初始化
      this.scroll = new BScroll(this.$refs.wrapper, {
        probeType: this.probeType,
        click: this.click,
        scrollX: this.scrollX
      });

      // 是否派发滚动事件
      if (this.listenScroll) {
        this.scroll.on("scroll", pos => {
          this.$emit("scroll", pos);
          // 下拉动作
          if (pos.y > 50) {
            this.topTip = "释放立即刷新";
          }
        });
      }

      // 是否派发滚动到底部事件，用于上拉加载
      if (this.pullup) {
        this.scroll.on("scrollEnd", () => {
          this.topTip = "下拉刷新";
          // 滚动到底部
          if (this.scroll.y <= this.scroll.maxScrollY + 50) {
            this.$emit("scrollToEnd");
          }
        });
      }

      // 是否派发顶部下拉事件，用于下拉刷新
      if (this.pulldown) {
        this.scroll.on("touchEnd", pos => {
          // 下拉动作
          if (pos.y > 50) {
            this.$emit("pulldown");
          }
        });
      }

      // 是否派发列表滚动开始的事件
      if (this.beforeScroll) {
        this.scroll.on("beforeScrollStart", () => {
          this.$emit("beforeScroll");
        });
      }
    },
    disable() {
      // 代理better-scroll的disable方法
      this.scroll && this.scroll.disable();
    },
    enable() {
      // 代理better-scroll的enable方法
      this.scroll && this.scroll.enable();
    },
    refresh() {
      // 代理better-scroll的refresh方法
      this.scroll && this.scroll.refresh();
    },
    scrollTo() {
      // 代理better-scroll的scrollTo方法
      this.scroll && this.scroll.scrollTo.apply(this.scroll, arguments);
    },
    scrollToElement() {
      // 代理better-scroll的scrollToElement方法
      this.scroll && this.scroll.scrollToElement.apply(this.scroll, arguments);
    }
  },
  watch: {
    // 监听数据的变化，延时refreshDelay时间后调用refresh方法重新计算，保证滚动效果正常
    data() {
      setTimeout(() => {
        this.refresh();
      }, this.refreshDelay);
    },
    noMoreData(val) {
      if (val) {
        this.bottomTip = "没有更多数据了";
        this.scroll.closePullUp();
      } else {
        this.bottomTip = "";
        this.scroll.openPullUp();
      }
    }
  }
};
</script>

<style scoped>

.bottom-tip {
  height: 80px;
  line-height: 80px;
}
</style>

```

> 我们在使用的时候，就可以直接像下面这样使用：

```
<scroll  class="wrapper"  :data="cardList" :pulldown="false" @scrollToEnd="scrollToEnd">
        <div class="wrapped"></div>
</scroll>
```

### 寻找替代

> 其实问题很简单，因为一体机内部普通的浏览器滚动，是没问题的，因此思路是只需要将scroll组件抹去，然后，使用浏览器的滚动方式。

> 但是这样又引入了另一个问题，就是关于滚动条的美化，目前项目里滚动条的美化是使用jq插件，nicescroll，但是这种东西，有一个问题，首先vue里使用jq，确实不够简洁，其次，这东西不是每次初始化都能成功，即使是在mounted的nextTick的回调里，因此我们需要找到一个合适的替代的东西。

```js
//这种方式不是百分之百靠谱，存在缺陷
mounted(){
    this.$nextTick(function(){
      $('#main').niceScroll({
        cursorcolor: "#d7d7d7"
      });
    })
  },
```

> 当我在百度的时候，无意中发现，竟然还有el-scrollbar这样一种东西，但是貌似我在elementUI文档上面，没见过这个家伙啊，后来仔细了解才知道，这个其实是官方文档未指明的组件，但是确实在select组件的下拉里，确实有这样的一个美化滚动条的操作，下面来看下使用方式：

### 美化滚动

```
<div class="scroll" >
            <el-scrollbar ref="scroll">
                <ul class="list f26">
                    <li v-for="(item,index) in list" :key="index">
                        <div class="info clearfix">
                            <div class="fl tc ell">{{item.trainNum}}</div>
                            <div class="fl tc ell">{{item.departureStation}}-{{item.arrivalStation}}</div>
                            <div class="fl tc ell">{{item.trainType}}</div>
                            <div class="fl tc ell">{{item.drivingTime}}</div>
                            <div class="fl tc ell">{{item.arrivalTime}}</div>
                        </div>
                        <div class="line">
                            <div class="line-left tc fl">途经站点</div>
                            <div class="line-right">
                                <div v-if="!item.halfwayStation" class="no-data-sp tc">--暂无数据--</div>
                                <div v-else>{{item.halfwayStation | arrowAdd}}</div>
                            </div>
                        </div>
                    </li>

                </ul>
            </el-scrollbar>
        </div>
```

> 上面代码可以看出来，结构很简单，需要一个外包裹层，定高，然后有个组件层e--scrollbar，里面就是滚动的内容。要注意的就是样式的设定：

```
.scroll {
    height: 715px;
    overflow: hidden;
}

.el-scrollbar {
    height: 100%;
}

/deep/.el-scrollbar__wrap {
    overflow-x: hidden; 
}

```
> deep属性可以在我们style设置scoped属性的时候，去覆盖外层的样式，这里我们需要设置下.el-scrollbar__wrap的样式，横向滚动为hidden，因为不设置的话，会出现一个横向滚动条。

> 按照上面一顿骚操作，我们可以看出美化的效果了：

![](https://github.com/bettermu/blog-picture-store/blob/master/20191112/1.jpg?raw=true)

### 完善交互

> 既然美化的问题搞定了，那么剩下的，就是如何去完成better-scroll组件的滚动加载了，其实原理是想通的，在没有better-scroll组件之前，我们都是监听浏览器本身的滚动，来进行数据的加载交互，这里我们只需要监听滚动元素的scroll事件就可以了。

> 但是仔细看下上面的结构，我们如何去获取发生滚动事件的这个元素呢？截图我们可以发现，实际上，el-scrollbar的作用，还隔了两层元素，真正的滚动，是触发在wrap这个元素上的：

![](https://github.com/bettermu/blog-picture-store/blob/master/20191112/2.png?raw=true)

> 当时我在想，el-scrollbar这个好获取，通过this.$refs属性就可以：

```
console.log(this.$refs.scroll)

```

> 那如何去获取，wrap这个对象？我们试着打印一下el-scrollbar吧：

![](https://github.com/bettermu/blog-picture-store/blob/master/20191112/3.png?raw=true)

> 上图我们可以看到，this.$refs.scroll里还有$refs属性，里面有个wrap对象，就是我们需要找的触发滚动的元素！

> 我们可以写出下面这样的监听代码：

```

mounted(){
      this.$nextTick(function(){
        let scroll = this.$refs.scroll
        scroll.$refs.wrap.addEventListener('scroll',function(){
          
        })
      })
    },
```

> 那么有了监听代码，下面我们来实现具体的实现逻辑，首先，要判断，何时滚动触底，然后进行数据加载，其次，我们需要进行节流处理，因为滚动事件触发频率太高，需要人为进行节流，我们这里实现的思路就是采用标志位，请求前，关闭开关，请求回来之后打开开关。当数据全部加载完，则不发请求。

> 正常说来，我们做PC端的滚动加载的时候，我们需要三个值，分别是视口高度，页面高度，以及滚动距离，其具体判定条件如下：

```
  页面高度  <=  视口高度 + 滚动距离
```

> 上面的公式，是当body滚动的时候，判断页面触底的方式，那么在我们这里，需要进行一下转换，我们需要使用滚动内容高度，代替页面高度，容器高度，代替视口高度,同时呢，我们对照看下掘金等网站的滚动加载操作，都是未至底部便进行数据请求，其实只需要加一个数值就可以：

```
    内容高度  <=  容器高度 + 滚动距离 + 50  //这里的50只是参考，作为提前加载的阈值
```

> 具体的逻辑实现如下

```
let scroll = this.$refs.scroll
scroll.$refs.wrap.addEventListener('scroll',function(){
          if(this.scrollTop + scroll.$el.clientHeight + 50 >= this.scrollHeight){
            //函数节流操作
            if(!that.flag){
              that.flag = true
              that.currentPageNo++;
              that.getList()
            }

          }
        })
```
> scrollHeight实际上就是获取滚动内容的高度，$el实际上获取的就是组件元素的dom节点，我们可以通过clientHeight去获取它的实际高度，这里就是所谓的容器高度，scrollTop就是滚动条距离容器顶部的距离，也就是滚动距离了。接下来就是节流的常规操作，我们要记得在getLIst请求回来的时候，要把开关重置即可，如果没有更多数据了，则直接关掉开关就可以。

### 总结

> 上面说了这么多，也算是有点感慨，其实很多东西，不细心根本没法发现，当初就是不知道elmentUI有这种美化滚动条的东西，所以才用了niceScroll，还引入了jquery，这骚操作其实挺恶心，现在想来，以后在Vue项目里，如果是PC，那el-scrollbar真的是美化滚动条的最好选择，前提是，项目的UI框架使用的是elementUI,否则，还是要另寻他法，不然为了一个滚动条美化，还要去引入一个大的UI框架，是在是不值得。

> 另外其实这个项目要解决的问题，首先是交互的问题，其次才是滚动条的美化，但是滚动条美化，又是必要的需求，可以简单总结出，交互是重要问题，美化是必要问题。





