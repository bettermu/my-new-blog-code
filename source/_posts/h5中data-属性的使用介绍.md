---
title: h5中data-*属性的使用介绍
date: 2020-01-06 22:58:03
tags:
  - html
  - data-
  - jq
categories:
  - html
  - data-
  - jq
---


> 在HTML5中，添加了data-*的方式来自定义属性，所谓data-*实际上就是data-前缀加上自定义的属性名，命名可以使用驼峰格式，但是取值必须要是全部小写（后面会说到原因，如果非要使用驼峰去获取，该怎么去写），使用这样的结构可以进行数据存放，下面来说一下，它的基本的使用方式：

1. html绑定的写法
    1. 简单单词：
        ```html
            <div id="testDiv" data-cd="24">
                Click Here
            </div>
        ```
    2. 使用驼峰命名（这里需要特别注意的地方）:
        ```html
            <div id="testDiv" data-cartCd="24">
                Click Here
            </div>
        ```

> 我们可以看到，上面的两种绑定方式，第一种是小写字母，第二种是驼峰式，那么这两种在取值的时候，又有何区别呢？

2. 取绑定的值的方法
    1. 原生js的取法：
        ```js
            //原生的dom对象上，有个dataset对象，里面包含了以data-为前缀的所有自定义属性的键值
            
            var testDiv = document.getElementById('testDiv');
            console.log(testDiv.dataset.cd); //'24'  简单的单词取法
            console.log(testDiv.dataset.cartcd)  //'24' 这里要注意，大写的'C'需要写成小写才能获取到，否则就是undefined
        ```
    2. jq的取法
        ```js
            //jq在原生的基础上，做了一层封装，我们可以通过jq封装的dom对象来获取，jq封装的dom对象上，暴露了一个data()的方法，参数就是我们自定义的属性值，即 data-后面的字段名
            console.log($("#testDiv").data("cd"));//'24'
            console.log($("testDiv").data("cartcd"));//'24' 这里痛原生取法一样
        ```
3. 修改绑定的数据
    1. 原生js方式
        ```js
            //直接赋值
            testDiv.dataset.cartcd = '新值'
        ```
    2. jq的方式
        ```js
            //data()方法的第二个参数，为新值
            $(“testDiv”).data(“cartcd”,”新值”)
        ```
    
> 上面基本介绍了这个自定义属性的用法，其实这个应用场景还蛮多的，比方说：

* 我们渲染列表，点击列表，可以跳转对应项目的详情，实际上我们可以通过渲染的时候，把id之类的值通过data-id绑定到模板标签上，再通过js去获取该属性，再去做相应的跳转逻辑。
* 以前我们绑定一些数据的时候，通常都会使用隐藏的输入框去做。有了data-*，我们再也不用通过一个hidden的input的value去绑定数据，减少了页面的元素数量，简化dom结构，提升渲染性能.

> 其实说白了就一点，配合js做更好地数据交互。

> 当然，在前端框架比如vue，react里我们很少会用到，因为它们拥有属于自己的一套数据流控制，比如，props这样的一个东西，它能够完成的功能和发挥的作用，远远比data-*要强大的多，但实际上，在一些小场景，甚至是一两个页面中，还是不建议引入一个框架的，基本上，js原生就可以满足大多数的需求。


> 另外，可能有些同学，会比较在意，我先前说到的，如果非要通过驼峰去取值，那怎么才能取到，而不是undefined呢？

> 在这里啊，要跟大家说一个小知识，就是我们一般写data-的时候，常用都是data-xxx，一般都是这种形式，而这种形式呢，我们在xxx的占位里无论写大写还是小写，取值的时候，这部分就必须按照全部小写的规则去取。那么，肯定还有另外一种规则，是的，还有一种规则，我们可以写出data-xx-xx的形式，那么看到这里，我想聪明的你已经明白了：

> woc，这种方式有些熟悉啊，习惯了使用vue框架来开发的你，肯定喜欢自定义组件吧，那么在Vue的自定义组件名称中，命名和引用，是要经过一层转换的：

```vue

<test-component></test-component>

import testComponent form './components/test-component'

export default {
    components:{
        testComponent
    }
}

```
> 连字符转驼峰，其实就是匹配 - 字符之后的第一个字母，将其转换成大写：

```js
    let str = 'str-arr-test';
    str = str.split('-').reduce((acc, val, idx) => idx === 0 ?
        acc + val : acc + val[0].toLocaleUpperCase() + val.slice(1), '');
    console.log(str)
```

> 驼峰转连字符，可以使用正则匹配的方式，匹配出所有大写字母的位置，替换成-小写的形式：

```js

    let str = 'strArrTest';
    str = str.replace(/([A-Z])/g, function ($1) {
        return '-' + $1.toLocaleLowerCase();
    });
    console.log(str);
```

> 那么，话说回来，如果我们必须要用驼峰的方式，去获取data-*的属性值，那么我们就需要在设值的时候，通过连字符短划线-的方式去命名，比如：

```html
<div id='testDiv' data-card-cd='24'>
```

```js
var testDiv = document.getElementById('testDiv')
console.log(testDiv.dataset.cartCd);  //'24'
```

> 我们可以在网页上面证实一下结果：

![](https://github.com/bettermu/blog-picture-store/blob/master/20200106/1.png?raw=true)

![](https://github.com/bettermu/blog-picture-store/blob/master/20200106/2.png?raw=true)
    
    
> 由上图我们可以看出来，我们在元素上绑定了data-id-id的属性，顺利的通过dataset.idId的方式，获取到了属性值。简直就是强迫症患者的福音（手动狗头）



