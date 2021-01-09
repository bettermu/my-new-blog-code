---
title: vue-awesome-swiper如何竖向展示多条数据
date: 2018-12-23 19:19:35
tags:
  - vue
  - vue-awesome-swiper
categories:
  - vue  
  - vue-awesome-swiper
---


在最近的项目里，有个功能需求，设计页面如下图所示：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181223/1.png?raw=true)

如上图所示，banner 图的底部有个网站公告的信息栏，需求是竖向定时滚动，并且一行最多展示三条数据，多出的部分，则排入下一行展示。并且每条都可以点击进入对应的详情页面。其中后台给的数据结构是下图这样的，就是一个简单的数组，数组里面是每个信息的具体对象。

![](https://github.com/bettermu/blog-picture-store/blob/master/20181223/2.png?raw=true)

仔细分析下，这样的需求其实很常见，因此，既然使用了 swiper 这个组件，那么我们就去 swiper 组件的[官网](https://www.swiper.com.cn/)去找下符合需求的解决方式，其实之前在做别的项目的时候，实现过类似的需求，也就是 swiper 滚动的分组，每一轮里面包含多少个 slide，官网里配置如下两个属性，设置为 3，就能够实现上面的需求：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181223/4.png?raw=true)

但是实际上，效果相差十万八千里啊，如下图：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181223/3.png?raw=true)

最关键的一点是，我们是纵向滚动的，而和官网给的例子，是不太一样的，官网的是排列方向与滚动方向一致，而我们这里，是不一致的，因此造成了布局上的差异。

官网文档我还没有很仔细的尝试，具体还有没有可解决的方式了，目前我没去尝试，但是解决这个问题，可以有别的方式，其实可以从后台给的数据入手，后台接口给我的数据是个一维数组，但是我需要的是每 3 条一个小数组，外面嵌套一个大数组的二维数组，因此，我们可以做一下转换：

```js
    //这个函数主要是把一维数组处理成包含任意个数的小数组的二维数组
    handleNewsList(data) {
      //data是后台接口过来的一维数组
      var num = 3; //每个子数组里的元素个数
      //var arr = [1, 4, 5, 6, 34, 34, 67, 895, 2456, 87, 9, 5, 23, 884, 56];
      var Arr = new Array(Math.ceil(data.length / num));
      for (var i = 0; i < Arr.length; i++) {
        Arr[i] = new Array();
        for (var j = 0; j < num; j++) {
          Arr[i][j] = "";
        }
      }
      for (var i = 0; i < data.length; i++) {
        Arr[parseInt(i / num)][i % num] = data[i];
      }
      return Arr;
      //console.log(Arr);
    },

    //最后返回的Arr格式是[[...],[...],[...],...]
```

经过上面对于数据格式的处理后，很方便的，我们可以这样写html的结构
```html
  <!-- publicList是转换后的二维数组，并且子数组长度为3 -->
  <swiper v-if="publicList.length" :options="newsOption" class="news-swiper cp cfff">
    <swiper-slide :key="index" v-for="(item,index) in publicList">
      <span @click="tapToDetail(item[0].ncId,'首页','网站公告',item[0].title)" class="sb-title ell">{{item[0].title}}</span>
      <span @click="tapToDetail(item[1].ncId,'首页','网站公告',item[1].title)" class="sb-title ell">{{item[1].title}}</span>
      <span @click="tapToDetail(item[2].ncId,'首页','网站公告',item[2].title)" class="sb-title ell">{{item[2].title}}</span>
    </swiper-slide>
  </swiper>
```

经过上面，再加上样式的修改，我们可以正确得到所需要的展示效果了：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181223/5.png?raw=true)

并且可以发现，当最后一个数组，不满3条，也是可以正常展示的：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181223/6.png?raw=true)




