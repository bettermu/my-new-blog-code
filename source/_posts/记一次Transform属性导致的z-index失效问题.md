---
title: 记一次Transform属性导致的z-index失效问题
date: 2018-09-02 22:02:27
tags:
---



在最近的项目中，遇到了这样一个问题，页面中，有百度地图的图层，以及一个fixed定位的隐藏层，在页面进来后，点击按钮，显示fixed的隐藏层，但是当地图加载好之后，这个隐藏层就会消失掉。这个问题，真的是太奇怪了。

一开始，我以为是z-index的缘故，因为我确实没有给隐藏层加上z-index属性，所以，可能大概也许应该是z-index没设置的原因么？于是便将z-index值设成了9999最大，后来发现，还是不行，地图层依旧会把隐藏层给覆盖掉。

难道是百度地图的z-index比9999还要高么？？？查看了页面结构，发现，百度地图的z-index是0......，好吧，那会是什么原因导致了隐藏层的z-index失效呢？

其实，导致z-index失效的问题有很多种：

* 父标签position属性为relative
* 该标签有float属性

当然，所对应的解决方法也很简单：

* position:relative改为position:absolute
* 去除浮动属性

但是，经过我的仔细检查，发现，这次的z-index失效，并不是上述两个原因导致的，与上述两个原因没有任何的关系。那么通过一顿搜索和查阅，发现，还有一种让z-index失效的可能性，就是CSS3的Transform。

项目中，所引入的百度地图，可能里面确实使用到的css3 transform的属性，所以才导致了z-index不生效吧。那这其中的原理是怎么样的，为什么会产生这种奇怪的问题呢？

这里涉及到一个 stacking context (层叠上下文)的概念。

给元素设置transform属性会创建一个新的stacking context。

看下面的代码：

```html
<div class="test test-1">
</div>
<div class="test test-2">
</div>
```

```css
.test {
    width: 100px;
    height: 100px;
}

.test-1 {
    -webkit-transform: scale(.9);
    transform: scale(.9);
    /*opacity: 0.9*/
    background: #f20;
}

.test-2 {
    margin-top: -50px;
    background: #000;
}
```

上面这个例子中，两个div都没有设置任何position，如果没有给test-1添加transform属性的话，第二个div将会覆盖第一个div。但是如果设置了transform的话呢？由于transform会创建一个新的stacking context。在层级关系上就要比test-2高一级，因此，显示在上面。

那么再看下面的代码：
```html
<div class="test test-1">
</div>
<div class="test test-2">
</div>
```

```css
.test {
    width: 100px;
    height: 100px;
}

.test-1 {
    position: relative;
    background: #f20;
}

.test-2 {
    -webkit-transform: scale(.9);
    transform: scale(.9);
    margin-top: -50px;
    background: #000;
}
```

这个例子是对上面那个例子作了个简单的修改。我们给test-1添加了一个position: relative，test-2没有任何position属性，只是添加了一个transform的属性。如果不看预览页面的话，可能会以为test-1会显示在test-2上方，其实不然。由于transform会创建新的stacking context，同时test-2在文档中又处于test-1的后面，所以最终的效果是test-2显示在test-1的上方。

通过上面两个例子，我们可以认为，导致这次z-index失效的原因，是因为，百度地图使用了transform，进而导致了产生了新的层叠上下文，由于地图层比隐藏层靠后生成，因此，无论隐藏层设置多大的z-index，地图层都会盖住隐藏层，所以，导致z-index失效，页面就出现了bug，那么如何去解决这样的问题呢？

重点来了，我们知道，设置元素z轴位置的，可不止z-index一种，而且，z-index必须是要求元素有非static定位，才能生效。那么，还有一种方式么？

我们简单思考下，既然是tranform属性，创建出的层叠上下文，导致了z-index失效，那么我们可以用以毒攻毒的方式，使用transform的某个属性，来给隐藏层同样创建出层叠上下文啊，这样，就不会存在层级上面的落差了，其实正确答案是有的，transform里的translate属性，有三种，其中translateZ,就是调节元素相对于z轴层叠顺序的：

```css
ele {
  transform:translateZ(300px);
}

```

设置的值越高，其图层显示位置也越优先。

比如当前这个问题，百度地图层调用了transform导致了地图层的层级变高，那么默认的，可以认为是被设置成了translateZ(0)

那么，只要我们在隐藏层元素上，同样设置translateZ属性，但只要值比0大，那么，肯定都是要比地图层高的，那么，隐藏层被地图层给遮掉的问题就不会有啦

感兴趣的同学私底下可以试试，使用translateZ替代z-index来解决因为Transform属性导致z-index失效的问题，是很简洁靠谱的。
