---
title: 百度地图在项目中使用的js小优化
date: 2018-07-12 21:50:44
tags: 
  - js 
  - 百度地图
categories:
  - js
---



在最近的项目里，有一个地图模式的展示页面，需要根据当前用户的类别选择，分别展示不同的类别下的数据，当然，地图还是使用的百度地图api，具体的交互方式如下图所示： 

![](https://github.com/bettermu/blog-picture-store/blob/master/20180712/1.png?raw=true)

当点击箭头所指的tab切换时，就要进行相关数据的获取和更新。问题是，一开始进来获取数据，和用户交互获取数据，我采取的是同一个方法getListByType，如下图所示：  

![](https://github.com/bettermu/blog-picture-store/blob/master/20180712/2.png?raw=true)  

因为，如果为这两种获取数据方式而分开写获取的方法，也显得太过冗余，但是，首次获取数据，是在获取数据后，才能初始化地图，但若是使用上图所示的代码，同样用于去完成动态获取类别数据的话，会有很大的性能问题，因为每次都会去调用地图的初始化，地图的初始化代码如下所示，那么就意味着，每一次的请求，都会造成地图的重新初始化，这样违背了最基本的原则，性能之上，这种处理方式肯定是不可取的。    

![](https://github.com/bettermu/blog-picture-store/blob/master/20180712/3.png?raw=true)   

那么如何在这一公用的方法里，去区分页面一开始获取的数据还是用户操作动态获取的呢？  
其实很简单，因为如上图所示，map对象里，有一个mapObj的字段，用来存储地图的实例对象，那么一开始加载的时候，地图对象是没有被初始化的，因此mapObj仍然为null值，当初始化之后，mapObj就变成了百度地图的实例化对象，那么在用户交互获取数据的时候，mapObj就不可能为null,因此，通过判断mapObj对象是null还是地图实例对象，来进行不同的加载操作。   

如图：  

![](https://github.com/bettermu/blog-picture-store/blob/master/20180712/5.png?raw=true) 

如果是null，则进行map.init()操作  

如果不是null，则进行map.update()操作.update方法代码如下，其实也就是进行地图覆盖物的常规操作，先进行点清除，再进行点的重新渲染:   

![](https://github.com/bettermu/blog-picture-store/blob/master/20180712/4.png?raw=true)

别看这样的操作很简单，但其实，代码的质量，大部分取决于细节方面的展现，虽然，真的可以，map.init()确实也可以实现，每次数据的刷新和展示，但是，每次都创建一个地图实例对象，耗费的性能是很大的，倒不如进行一个区分判断，这样对于性能能够有较大的提升，并且在用户体验上，也能有个较大的提升和优化。
