---
title: 全景框架krpano.js介绍分享
date: 2019-07-26 23:05:23
tags:
  - krpano.js
  - 全景漫游
categories:
  - krpano.js
  - 全景漫游
---


# 全景框架krpano.js介绍

## 概览

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/13.png?raw=true)


## 一、全景vr概念

> 在2016年上半年的这个时候，VR、AR概念炒得很火。室内，室外景观等行业，均利用这波热潮，借全景的力来拓展自己的市场，我们旅游业务线当时也基于目前的需求，希望能让游客不出家门，只用浏览器，便能提前了解景点情况。打算做一套自己的全景旅游导览系统，这也就是我今天打算分享的由来。

> 全景漫游（英文：panorama）技术可以让体验者在全景图像构建的全景空间里切换视角的浏览。它是通过拍摄全景图像，再采用计算机图形图像技术构建出全景空间，让使用者能用控制浏览的方向，或左或右、或上或下观看物体或场景，仿佛身临其境一般。与传统的3D建模相比，全景漫游技术制作简单，数据量小，系统消耗低，且更有真实感。


## 二、需求分析和解决方案
> 有了想做一个自己的全景系统这样一个想法之后，就得想办法如何去实现它，那么首先，我们参考了业界流行的解决方案，比方说，在当时很流行的[720云](https://720yun.com/)等全景制作平台，当时刚打开他们的网站，确实被惊艳到了，原来浏览器可以展示这么酷炫的效果，下一秒就开始好奇，他们是如何做到的。虽然他们提供了一套的制作流程，但是我们的需求是自己搞一个高度定制化的全景系统。所以职业习惯，下意识的我便按下了F12，找到source面板，在一堆文件夹下面开始找，终于给我找到了： 

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/1.png?raw=true)

> 上图可以看到注释......原来他们用的是krpano.js 顺藤摸瓜便打开了[krpano.js的官网](https://krpano.com/),打开后，刚开始一脸懵逼，全部都是英文，原来开发框架的是德国人，关键点是，框架收费的，129英镑，人民币1000多，但是项目组任性出钱买了下来（因为没有买版权，做出来的场景是带满屏的水印的，但其实到最近这一两年我们才发现，竟然有注册机可以破解版权了，莫名感觉有点亏了）

> 2016年当时其实还没有krpano相关的中文文档可以查阅，唯一的文档就是全英文的官网，而且，将近2000的api，可以说是巨量的，当时也是边做边学。不像现在，入门都有中文文档，有个[krpano中文网](http://www.krpano360.com)，个人维护的，质量不错，入门足够。

## 三、krpano.js简介

> krpano是一款全景漫游制作软件和工具。其具有以下特点：
* 高度灵活、性能卓越的轻量化全景漫游浏览器。
* 兼容HTML5和Flash，支持Webgl下的WebVR展示。
* 使用专用的krpano xml代码编写全景漫游，可开发出高度定制化的项目，也可利用krpano工具开发在线全景制作及展示平台。
* 支持多种类型的全景图以及全景视频和环物全景。
* 支持多种投影模式。
* 同时提供简单高效的批处理方式，可在无需代码干预下迅速生成一个基本功能兼备的全景漫游项目。

## 四、原理

> 其原理主要就是利用全景图制作工具，将一张全景图，制作成6张图片切片，这6张图片分别对应六面体的前后左右上下，六个方位，人的视角在六面体的中间位置，四周形成720°球面坐标系。  

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/3.png?raw=true)

> 切片的特殊地方就在于边缘的虚化效果，使得过渡非常自然，如果我们把切片替换成未处理的图片，那么会出现如下的效果，至少能看的出来，其实就是图片拼接出来的六面体而已：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/2.png?raw=true)


## 五、应用

> 那么应用的话呢，主要看你需求了，单纯的展示，就很简单，去业界主流的全景制作平台，或者，使用krpano自己的制作工具来进行制作。如果是想定制化一个系统或者产品，那就得深入krpano的文档进行学习了。

### 一、单纯的展示
> 业界的全景制作平台流程就不说了，基本都是傻瓜式的操作，下面给大家简单介绍一下，官网下载的包里都有些什么东西，首先放一张图,大概看下结构：

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/4.png?raw=true)

#### examples文件夹
> 这是krpano的案例文件夹(1.19的examples文件夹在viewer文件夹下)，这些案例都是学习krpano代码的很好的素材。  

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/8.png?raw=true)

#### templates文件夹
> 官方一系列模版所在的文件夹，包括了图片、xml、html以及相关配置文件等。通过对这里的了解和修改，我们可以做出自己的皮肤模版，然后一键生成，实现批量的工作流。不过正常情况下，我们通过批量处理工具，会自动生成，不需要从这里拷贝。

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/9.png?raw=true)

#### viewer文件夹
> krpano 下载包的官方插件、引擎、案例的存放位置，同时也是让案例文件夹能够正确显示的 viewer 所在的位置。其中plugins是当前版本的所有官方插件；examples是官方提供的案例。 

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/10.png?raw=true)

#### droplet.bat 文件
> Krpano Droplets 是krpano命令行工具加上配置文件config的一个快捷方式。droplets使用方式非常简单，将文件（通常是图片或者xml等文件）直接拖放在droplet图标上松开即可。

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/11.png?raw=true)

* MAKE PANO开头的，是只制作全景图片，并不整合成漫游文件夹
* MAKE VTOUR开头的，是制作全景图片，并且将他们整合到一个虚拟漫游中
* 我们一般常用带有MULTIRES的处理文件，因为这样会生成所有类型的全景图像，而NORMAL只能生成360°的全景图像

#### krpano Tools.exe

> 用来加密保护全景项目的可视化工具，需要注册才可以使用  

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/6.png?raw=true)

#### krpano Testing Server
> krpano 自带的静态文件本地服务环境，使用它可以路过本地文件案例限制来查看 Flash 与 HTML5 效果，。我们如果想要本地查看效果的时候，需要启动它，然后再打开对应的案例的html文件。  

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/5.png?raw=true)

#### 生成文件说明
> 通过使用上述的droplet.bat文件，也就是将全景图拖到droplet.bat文件上面，会打开控制台，等程序跑完，就会出现一个vtour文件夹，文件夹内容如下：

```
vtour/
| -- panos/             #存放全景切片图片的文件夹
| -- skin/              #存放皮肤相关文件
| -- plugins/           #用来存放插件
| -- tour.swf           #krpano flash viewer
| -- tour.js            #krpano HTML5 viewer
| -- tour.xml           #生成全景的相关配置
| -- tour.html          #用来浏览全景的页面，需要本地服务环境
| -- tour_editor.html   #添加热点（hotspot）与初始化视角设置的编辑器
| -- tour_testingserver.exe #windows系统中打开测试服务器并打开html文件进行本地浏览。
| -- tour_testingserver_macos #mac系统中打开测试服务器并打开html文件进行本地浏览。

```

### 二、自定义

> 说是自定义，其实代码结构和工具生成的方式一样，只不过形式上有所区别，下面，工具生成的代码结构，给大家讲解下，它究竟是如何运行的。

#### 1、代码结构

##### tour.html
```html
<div id="pano"></div>
<script src="tour.js"></script>
<script>
    embedpano({
        swf: "tour.swf", //有则表示加载flash引擎，如果设置html5:only则不需要该值
        xml: "tour.xml", //启动时的配置文件
        target: "pano", //要渲染到的目标容器ID
        html5: "only", //如果有需要用到flash，可设置为auto
        //id: "krpanoSWFObject", //默认的krpano对象，每一个viewer对应唯一id，与JS交互时要用到
        mobilescale: 1.0, //移动设备缩放，1表示不缩放，默认0.5
        passQueryParameters: false //是否接受URL传参，例如：tour.html?html5=only&startscene=scene2
    });
</script>
```

##### tour.xml
```xml
<krpano version="1.19"     //版本号
        onstart=""         //进入漫游时，xml加载和解析后执行的动作
        basedir="%FIRSTXML%" //设置程序根目录
        bgcolor=""      //全景播放器的背景色，不设置则为透明
        colorcorrection="default" //改变Flashplayer 10的色彩修正设置(Flash only)　可选的值：default、on、off
        logkey="true"     //当为Ture时，按O可查看log日志
        strict="false"            //严格代码模式
        showerrors="true"  //是否输出错误信息
        debugmode="false"  //当为True时，显示来自插件中的trace(0,text)中的text信息
    >
    ...
</krpano>
```
> krpano作为XML文件唯一根元素，必须设置！

##### XML文件配置

```xml
<krpano>
    <include>  //xml文件的相互引用，比如皮肤xml等
    <preview> //预览，加载预览图，渲染优化
    <image> //六个方位的切片图
    <view>  //具体场景的视图，对应六面体的六个方位
    <autorotate>  //场景自动旋转
    <plugin>  //插件 背景音乐，陀螺仪等
    <layer>    //图层，比如控制器
    <hotspot>  //切换场景热点
    <events> //事件响应
    <action> //交互指令
    <scene> //场景
</krpano>
```

#### 2、渲染处理

> a、首先，我们需要根据UI设计图，参照文档API，来对xml模板进行修改处理，来达到我们自己想要的那种效果。

> b、然后呢，我们会将主xml和公用的xml，放到后台java目录webapp下面的resource文件夹，这样，我们在js读取的时候，只需要采用相对路径即可

> c、动态场景所需要的对应的xml，只能由后台生成，后台有一套场景模板，和一套对xml进行节点操作的处理逻辑，然后将xml路径以正常的ajax请求返回给前端，前端进行场景加载等操作

> krpano渲染的方式如下，可以看到，主要的就是tour.xml文件，其他诸如插件，皮肤之类的，也是xml，不过将会被include标签引入tour.xml

![](https://github.com/bettermu/blog-picture-store/blob/master/20190719/7.png?raw=true)
#### 3、交互处理

> 既然是浏览器展示型的框架，那么交互一定是不能少了，你们一定很好奇，那像上面那样说的，krpano通过xml配置进行渲染，而js脚本又是单独的，那么如何去完成统一的交互呢？说白了，也就是说，如何去完成krpano和js脚本之间的通信？其实有几种方式：

##### krpano内置指令
```
<layer name="skin_btn_comment"  onclick="skin_add_comment()" />

<action name="skin_add_comment">
    set(hotspot[get(hotspotName)].width, '30px');
    set(hotspot[get(hotspotName)].height, '30px');
</action>

```
> 如上代码，layer是krpano内置的图层标签，类似于地图交互上的覆盖物，action呢之前也提了，表示声明一个交互指令，layer上可以绑定各种各样的方法，具体可以参考官网文档，这个方法的值，就是action标签name值的函数调用，当我们点击这个layer的时候，action包裹的各种操作就会被触发，action里包裹的便是krpano的内置语法处理。

##### call的方式

> 所谓的call方式，其实和js里call,apply的语义是类似的，也就是借调，这里的call，就是指krpano和js脚本互相调用。

> 其实大部分的场景，我们是需要这样的一种通信方式的，比如参数传递，数据存储之类的，很多时候，krpano的操作，需要用到js脚本的变量，而自定义的页面元素，js脚本又得调用krpano的指令。

> 首先，我们来看下，krpano的xml中，如何调用js里声明的方法呢

```
//xxx.js

function callKrpano(id) {
    $("#vrHotspotID").val(id);
}

```
> 我们在js脚本里声明一个方法，这个方法接受一个id的参数

```
//xxx.xml

<action name="skin_add_comment">
    js(callKrpano(get(hotspotName)));
</action>

```

> 我们在xml里可以使用js(functionName)的方式，来调用js的方法

> 然后，我们再看下，js里调用krpano的语法

```

function loadPano(xmlPath) {
    var krpano = document.getElementById("krpanoSWFObject");
    krpano.call('loadpano(' + xmlPath + ')');
}

```
> 我们可以通过dom操作，获取到krpano对象，krpano对象里集成了一个call方法，这个方法接收的是字符串形式的kapano内置语法段，注意，如果有js变量，我们需要进行拼接。比方说上面表示，就是js执行krpano的加载场景的这个指令。


## 六、结语

> 其实今天时间不多，只能给大家讲个极其入门的大概，krpano这个全景框架，大家有兴趣可以去了解下，入门的话就去krpano中文网，如果想深入了解，建议还是慢慢看官网文档，全英文的，配合有道词典什么的，慢慢嗑。说实话，这东西过了这么长时间，没怎么用，很多东西都忘记了，只能到再用到的时候，再去边查边用，因为人的精力是有限的，合理的分配时间和精力，才是最重要的选择。








