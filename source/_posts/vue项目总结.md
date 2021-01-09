---
title: vue项目总结
date: 2019-01-31 21:38:42
tags:
  - vue
categories:
  - vue
---


最近刚完成第一个vue的实际业务项目，这里抽出一些时间，来对做这个项目的时候的一些经验和感悟，以及需要注意的地方，做个总结。

## 一、结合设计需求，完成组件的编写和复用

举个例子，比方说，下面这张图是资讯网首页的导航栏分类的交互动图：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190130/2.gif?raw=true)

而下面这张，是旅游风光页面下面的导航栏分类的交互动图：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190130/1.gif?raw=true)

可以看到，二者的样式布局，以及交互，基本是相同的，唯一的差别就是，旅游风光页面，多了一项交互，也就是鼠标移到某个栏目上的时候，需要在下方显示其对应的栏目列表，就在组件下方展示。

思路是这样的，当时在做的过程中，因为为了节省时间，达到组件最大化的复用原则，因此将导航栏效果作为单独的一个组件来开发，而另一个需要在下方展示对应列表的容器，是与导航组件同级的：

首页的组件，是这样的，传入一个数据列表props，用来进行数据展示
```
  <Blinds :blindsList="blindsList" />

```

而旅游风光页面的组件是这样的,多传了两个props，isBlinds用来给组件内部判断，是否是需要展示字列表的字段，而getActiveIndex这个是用来让子组件给父组件emit事件使用的，也就是，监听子组件当前鼠标移到哪一个元素的上面，记住index，然后，传给父组件，根据index去显示数据里对应的列表

```
<Blinds ftitle="栾川风光" :blindsList="blindsList" :isBlinds="isBlinds" @getActiveIndex="changeIndex"/>
```

其实一般来说，组件的复用基于props传参，为什么这么说呢，很多时候，实际情况里没有完全一模一样的模块，多少都会有些区别，我们需要抽离出这些区别，通过外部props来控制它们的表现形式，从而达到复用而多态的方式，比方说，这个项目里，光是简单的title标题，就有5种：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190130/3.png?raw=true)
![](https://github.com/bettermu/blog-picture-store/blob/master/20190130/4.png?raw=true)
![](https://github.com/bettermu/blog-picture-store/blob/master/20190130/5.png?raw=true)
![](https://github.com/bettermu/blog-picture-store/blob/master/20190130/6.png?raw=true)
![](https://github.com/bettermu/blog-picture-store/blob/master/20190130/7.png?raw=true)

从上面的几张图里可以发现，其实都比较类似，比如都有标题这个字段，样式也大多类似，这样就足够了，其他的不同的地方，交给props来控制，比如什么时候展示更多，什么时候，有下面的绿条，什么时候又会有哪些特殊的样式。

props给我们带来了更多的灵活性，我们可以方便的控制住组件的表现形式和交互效果，这也是vue组件使用的一个较为核心的知识。   

不过除了props来动态控制，我们还可以使用slot插槽的形式来完成以上多类似组件的开发，比方说上面的更多，绿条，以及其他的特殊样式，我们可以通过slot动态插入不同的样式元素，来做到动态的展示。也是最大化复用组件的一种有效方式。


## 代码层级的优化

### 不建议v-for 和 v-if 在同一个标签里使用：

```
<li v-for="item in list" v-if="item.active===0"></li>
```

比方说，上面的这句代码，实际上，vue会在渲染的时候，才去进行判断，这样会造成一些性能损耗，特别是在大量数据的时候，我们需要做的优化就是，尽量将数据处理提前，而不是放入渲染阶段来进行，比如我们可以在拿到list数据中之后，可以通过computed属性来进行数据过滤：

```
computed:{
  activeList(){
    return this.list.filter((item)=>item.active === 0)
  }
}
```

之后我们直接使用activeList来渲染列表就可以了：

```
<li v-for="item in activeList"></li>
```

### v-if和v-show的使用场景

二者之间的区别是，v-if是懒加载，只有值为true的时候，才会进行渲染加载，而为false的时候，元素根本不存在，就不会占用布局空间。而v-show无论状态是true还是false，都会进行渲染，并且占据空间，也就是说，我们是可以获取到元素的。

如何正确的分场景来使用它们呢？

举个例子，比方说，我们需要通过登录状态来显示对应的展示：

```
<span v-if="!isLogin">请先登录</span>
<span v-else>你好，XXX</span>
```
比如上面的代码，如果用户没登录，我们需要展示请先登录，如果用户登录了，我们需要展示的是你好，XXX，那么其实当前的场景，是有序性的，也就是说，不可逆，要么就登录，要么就没登录，不会出现两种状态频繁切换，因此，使用v-if来一步到位比较合适。但是下面这种情况：

```
<dialog v-show="isShow"></dialog>
```
弹窗是很常见的一种交互方式，我们知道，用户点击的时候，弹出，再点击相应区域或者取消按钮的时候，再隐藏，其实这是属于状态频繁切换的场景，因此，v-show比较适合。

另外呢，我们在v-if或者v-show的表示布尔值的表达式不适合特别长的，至少内联不能写成如下这样：

```
v-if="isShow && isAdmin && (a || b)"
```
这样的复杂判断，其实可以在computed计算属性里面写个方法，来return这个表达式。如果其他地方要复用这个判断逻辑，那么只需要使用这个computed属性就可以了:

```

<span v-if="isAdmin">请先登录</span>

computed:{
  isAdmin(){
    return this.isShow && this.isAdmin && (this.a || this.b)
  }
}

```

### 路由优化懒加载

懒加载也叫延迟加载，即在需要的时候进行加载，随用随载。在单页应用中，如果没有应用懒加载，运用webpack打包后的文件将会异常的大，造成进入首页时，需要加载的内容过多，延时过长，不利于用户体验，而运用懒加载则可以将页面进行划分，需要的时候加载页面，可以有效的分担首页所承担的加载压力，减少首页加载用时。

```
const router = new Router({
      routes: [{
          path: '/',
          name: 'home',
          component: Home
        },
        {
          path: '/guide',
          name: 'guide',
          component: () => import('./views/guide-tour.vue')  /* webpackChunkName: "guide-tour" */ 
        },
      ]
})
```

比方说上面的代码，就是项目里router.js的部分代码，可以看到，我们的home组件，使用的是正常加载，也就是，页面初次渲染就加载，而guide语音导览页面的写法则是当路由指向guide的时候，才会加载的异步写法。我们希望的是，访问哪个页面，再去加载，从而减轻首次加载的压力，这样我们再看下，实际的请求，也是按照我们的期待一样，分别跳转对应的页面，切换路由的时候，才去加载对应的脚本片段：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190130/8.png?raw=true)


### keep-alive的使用

vue2.0提供了一个keep-alive组件，用来缓存组件,避免多次加载相应的组件,减少性能消耗:

```
<keep-alive>
<component>
  <!-- 组件将被缓存 -->
</component>
</keep-alive>
```

我们实际需求里面，并不是所有的组件都需要进行缓存，有的需要即时更新的，那么我们就需要有选择性的进行缓存操作，router中有个meta配置，我们可以在template里这么配置：

```
// 这是目前用的比较多的方式
<keep-alive>
    <router-view v-if="$route.meta.keepAlive"></router-view>
</keep-alive>
<router-view v-if="!$route.meta.keepAlive"></router-view>
```
也就是，我们判断meta属性的boolean值，来进行选择性的渲染，keep-alive标签是不占据实际空间的，因此上面的方式是实现选择性缓存的较好方式。那么我们的router.js里应该这么写：

```
routes: [
    { path: '/', redirect: '/index',  component: Index, meta: { keepAlive: true }},
    {
      path: '/common',
      component: TestParent,
      children: [
        { path: '/test2', component: Test2, meta: { keepAlive: true } } 
      ]
    }
    ....
    // 表示index和test2都使用keep-alive
```

通过设置meta.keepAlive为true，来让组件进行缓存，当然如果没有该项配置的，则组件是不进行缓存的。



