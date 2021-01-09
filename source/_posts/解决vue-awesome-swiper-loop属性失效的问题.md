---
title: 解决vue-awesome-swiper loop属性失效的问题
date: 2018-12-25 20:56:19
tags:
  - vue
  - vue-awesome-swiper
categories:
  - vue  
  - vue-awesome-swiper
---

最近在项目里的轮播组件开发中，在使用 vue-awesome-swiper 这个组件的时候，遇到了一个问题，在轮播组件加载完成之后，有概率性的会出现 Loop 属性设置失效的问题，也就是轮播图不会无限轮播，原来的代码如下：

```js

<template>
  <div>
    <swiper :options="bannerOption" class="banner-swiper">
      <swiper-slide v-for="(item,index) in banners" :key="index">
        <a class="db" :href="item.url">
          <img :src="item.resourcePath">
        </a>

      </swiper-slide>

      <div class="swiper-button-prev" slot="button-prev"></div>
      <div class="swiper-button-next" slot="button-next"></div>
    </swiper>
  </div>
</template>


<script>
import "swiper/dist/css/swiper.css";
import { swiper, swiperSlide } from "vue-awesome-swiper";
export default {
  props:{
    banners:{
      type:Array,
      default:[],
    }
  },
  data() {
    return {
      bannerOption: {
        loop: true,
        observer: true,
        observeParents: true,
        autoplay: {
          delay: 3000
        },
        navigation: {
          nextEl: ".swiper-button-next",
          prevEl: ".swiper-button-prev"
        }
      }
    };
  },
  components: {
    swiper,
    swiperSlide
  }
};
</script>

```

后来经过思考，发现，其实组件的渲染是早于图片加载完成的，那么，就会出现设置 loop 的时候，组件内部还没有计算好实际的元素个数，导致 loop 失效，解决方法很简单，就是设法让 swiper 组件在图片数据加载好之后再开始渲染，那么需要在 swiper 这个标签上加上一段：

```js
v-if="banners.length"
```

意思也就是，当数据获取来之后，再进行组件的渲染，那么，就可以得到期望的结果了。调整后的代码如下：

```js
<template>
  <div>
    <swiper v-if='banners.length' :options="bannerOption" class="banner-swiper">
      <swiper-slide v-for="(item,index) in banners" :key="index">
        <a class="db" :href="item.url">
          <img :src="item.resourcePath">
        </a>

      </swiper-slide>

      <div class="swiper-button-prev" slot="button-prev"></div>
      <div class="swiper-button-next" slot="button-next"></div>
    </swiper>
  </div>
</template>

<script>
import "swiper/dist/css/swiper.css";
import { swiper, swiperSlide } from "vue-awesome-swiper";
export default {
  props:{
    banners:{
      type:Array,
      default:[],
    }
  },
  data() {
    return {
      bannerOption: {
        //freeMode: true,
        loop: true,
        observer: true,
        observeParents: true,
        autoplay: {
          delay: 3000
        },
        navigation: {
          nextEl: ".swiper-button-next",
          prevEl: ".swiper-button-prev"
        }
      }
    };
  },
  components: {
    swiper,
    swiperSlide
  }
};
</script>
```

