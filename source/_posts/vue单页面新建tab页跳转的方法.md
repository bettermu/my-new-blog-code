---
title: vue单页面新建tab页跳转的方法
date: 2019-01-14 20:11:16
tags:
  - vue
  - vue-router
categories:
  - vue
  - vue-router
---

最近在项目里遇到一个需求，列表页跳转到详情页的时候，希望可以另开一个页面，打开详情页。其实这个需求在传统的前端开发中十分常见，其实就是 a 标签加上一个—blank 的属性，表示新窗口打开。当然，js 也是有办法解决的，window 的 open 方法，第二个参数决定了采取什么方式去打开：

```js
window.open(url, "_blank");
```

上面的代码，也是可以完成的，但是我们这个项目是单页的，如何去获取这个 url 呢？其实在 vue router 的 api 里提供了这样一个方法：[官网地址](https://router.vuejs.org/zh/api/#router-resolve) router 实例有个 resolve 方法，用来解析路由，在现在这个场景中，只需要将想去跳转的页面的路由对象，当做第一个参数传入：

```js
let routerObj = this.$router.resolve({
  name: "detail",
  query: {
    id: id,
  }
});
console.log(routerObj)

```
返回值是解析后的一个对象，我们在控制台打印一下看看：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190112/1.png?raw=true)

可以看到，其中的href属性就是我们需要的，用来新建窗口的url，接下来，我们只需要这么使用就好了：

```js
let href = this.$router.resolve({
  name: "detail",
  query: {
    id: id,
  }
}).href;

window.open(href, "_blank");

```

其实想说的是，很多时候，我们不知道的东西，就会觉得做不到，但其实，如果对于文档稍微熟一点，对自己限制小一点，静下心来多想想，多去尝试尝试，会发现，其实问题很简单。




