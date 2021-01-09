---
title: vue-validate使用心得
date: 2018-12-31 16:46:23
tags:
  - vue
  - vue-validate
categories:
  - vue  
  - vue-validate
---


在最近的项目里，使用vee-validate表单验证组件的时候，出现了这样一个问题：

如图下所示的表单：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181224/1.png?raw=true)

当我在输入一些非法值的时候，表单是可以提示验证的，并且，在输入后删除，也是可以自动提示required必须的：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181224/3.png?raw=true)

但是，当我成功提交之后，会发现，当我把表单数据清空了，required提示却出现了：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181224/2.png?raw=true)

这样的方式，完全不符合需求的预期，无缘无故出现提示，也说不过去，所以，就查一查vee-validate的官方文档看看，虽然网上一堆vee-validate的使用教程，但是都很乱，并不完整，所以还是看官方的[vee-validate API 文档](https://baianat.github.io/vee-validate/api/validator.html#api)，才能找到正解。


简单分析下，我们现在的需求只不过是，要求在成功提交表单之后，把提示给去掉，用专业的说法，就是重置reset一下，所以，为了准确快速找到关键词，我就在全是英文的API文档中，搜索reset这个关键词，果然搜到了：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181224/4.png?raw=true)

从上图可以看出来，官方API里这个reset方法就是重置验证组件的作用，那么具体我们应该怎么使用呢，官方文档里是这么写的：

```js

this.$validator.reset()

```

这里的this，其实就是vue组件实力，我们在控制台里打印一下：

![](https://github.com/bettermu/blog-picture-store/blob/master/20181224/5.png?raw=true)

确实是有 $validator 这个属性的，那么很好办了，我们只需要在提交成功的回调里，加上这么一句话：

```js

this.$validator.reset()

```

就可以达到预期的效果了。





