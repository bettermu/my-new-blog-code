---
title: js中高德百度地图坐标转换
date: 2018-06-20 21:49:34
tags:
  - js 
  - 百度地图API
categories:
  - js
---


在最近所做的项目里，使用到了地图相关的模块，前台功能展示采用的百度地图，而后台开发采用的是高德地图，那么在前后端联调的时候，就遇到了一个问题，两家地图坐标的表现方式标准是不一样的，那么怎么去转换两者之间的坐标呢，那么当然啦，java后台本来就可以做一层处理方法，可以交由后端开发去完成，但是作为一名励志当全栈的前端来说，当然是在不影响前端交互性能的前提下，能由前端完成的，尽量前端完成，就算是数据处理好么，哈哈哈，下面就进入正题，关于如何处理两者坐标的转换？  

项目里，其实只需要将高德坐标转换成百度坐标，进行展示，那么高德转百度的方法代码如下：
```js
function gd_to_bd(gg_lng, gg_lat) {  
    var X_PI = Math.PI * 3000.0 / 180.0;  
    var x = gg_lng, y = gg_lat;  
    var z = Math.sqrt(x * x + y * y) + 0.00002 * Math.sin(y * X_PI);  
    var theta = Math.atan2(y, x) + 0.000003 * Math.cos(x * X_PI);  
    var bd_lng = z * Math.cos(theta) + 0.0065;  
    var bd_lat = z * Math.sin(theta) + 0.006;  
    return {  
        bd_lat: bd_lat,  
        bd_lng: bd_lng  
    };  
}  
```

举一反三，如果情况是反过来的，又怎么办么，所有情况毕竟都得考虑到嘛，所以，下面顺便提供下百度转高德的方法：
```js
function bd_to_gd(bd_lng, bd_lat) {  
    var X_PI = Math.PI * 3000.0 / 180.0;  
    var x = bd_lng - 0.0065;  
    var y = bd_lat - 0.006;  
    var z = Math.sqrt(x * x + y * y) - 0.00002 * Math.sin(y * X_PI);  
    var theta = Math.atan2(y, x) - 0.000003 * Math.cos(x * X_PI);  
    var gg_lng = z * Math.cos(theta);  
    var gg_lat = z * Math.sin(theta);  
    return {lng: gg_lng, lat: gg_lat}  
}
```

学习是日积月累的过程，每一步都需要记录下来，共勉~


