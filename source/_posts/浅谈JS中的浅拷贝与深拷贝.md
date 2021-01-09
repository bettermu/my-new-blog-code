---
title: 浅谈JS中的浅拷贝与深拷贝
date: 2018-04-13 09:00:51
tags:
  - js
categories:
  - js
---
　　前端工程师应该都比较熟悉浅拷贝和深拷贝的概念，在日常业务代码的过程中，特别是做数据处理的时候，经常行的会遇到，比如如何在不修改原对象的基础上，重新生成一个一模一样的对象，加以利用，又或是，如何巧妙地运用相关的内置API，来达成自己所需要的结果，比如数组相关的操作，splice和slice就是截然相反的处理，虽然同样是对数组进行截取操作，但是前者会影响原数组，后者则是返回一个新的数组对象，而对原来的数组并不会产生任何影响，这其中的差别，需要有一定的开发经验才能明白。   
好了，废话也不多说，下面来简单谈谈深拷贝与浅拷贝之间的那些事儿：  

## 一、什么是浅拷贝，什么是深拷贝
从名字上，就能看出来，这哥俩确实是有很大的区别，是对于复制方式的差别。浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存（内存区域没有隔离）。但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存（内存区域隔离），修改新对象不会改到原对象。在多层对象上，浅拷贝只拷贝一层，而深拷贝则会层层迭代，直到最后一层里只有 基本类型值，没有复杂类型的值，比如对象或者是数组。    
**浅拷贝举例**   
```javascript
  var Chinese = {
　　nation:'中国'
};
var Doctor ={
　　career:'医生'
}
function extendCopy(p) {
　　var c = {};
　　for (var i in p) { 
　　　　c[i] = p[i];
　　}
　　return c;
}
var Doctor = extendCopy(Chinese);
Doctor.career = '医生';
alert(Doctor.nation); // 中国
```

**深拷贝举例**  
```javascript
function deepCopy(p, c) {
　 var c = c || {};
　 for (var i in p) {
　　　　if (typeof p[i] === 'object') {
　　　　　　c[i] = (p[i].constructor === Array) ? [] : {};
　　　　　　deepCopy(p[i], c[i]);
　　　　} else {
　　　　　　c[i] = p[i];
　　　　}
　 }
　 return c;
}
```

## 二、深拷贝的实现方式

### ES6方法 Object.assign
ES6为我们提供了一种十分好用的方法，Object.assign(target, ...source)方法，assign方法接受多个参数，第一个参数target为拷贝目标，剩余参数...source是拷贝源。此方法可以将...source中的属性复制到target中，同名属性会进行覆盖，并且在复制过程中实现了'伪'深拷贝

```javascript
let foo = {
    a: 1,
    b: 2,
    c: {
        d: 1,
    }
}
let bar = {};
Object.assign(bar, foo);
foo.a++;
foo.a === 2 //true
bar.a === 1 //true
```
乍一看，好像已经实现了深拷贝的效果，对foo.a进行的操作并没有体现在bar.a中,但是再往后看
```javascript
foo.c.d++;
foo.c.d === 2 //true
bar.c.d === 1 //false
bar.c.d === 2 //true
```

Object.assign()的拷贝类型十分明显了,这是一种可以对非嵌套对象进行深拷贝的方法,**如果对象中出现嵌套情况,那么其对被嵌套对象的行为就成了普通的浅拷贝**.

如果真的想进行深拷贝,最简单粗暴地方式就是JSON操作.

JSON对象中包含两个方法, stringify()和parse(),前者可以将对象JSON化,而后者可以将JSON格式转换为对象.这是一种可以实现深拷贝的方法.

**但这种方法的缺陷是会破坏原型链,并且无法拷贝属性值为function的属性**

所以如果只是想单纯复制一个嵌套对象,可以使用此方法

```javascript
  let foo = {
    a: 1,
    b: {
        c: 1
    }
}
let bar = JSON.parse(JSON.stringify(foo));
```

### JQ中的$.extend
jQuery提供了一个可以用来做深拷贝的方法，就是$.extend

```javascript
  var $ = require('jquery');
var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};
var obj2 = $.extend(true, {}, obj1);
console.log(obj1.b.f === obj2.b.f);
// false
```

另外lodash也有提供_.cloneDeep来做深拷贝操作。
```javascript
  var _ = require('lodash');
var obj1 = {
    a: 1,
    b: { f: { g: 1 } },
    c: [1, 2, 3]
};
var obj2 = _.cloneDeep(obj1);
console.log(obj1.b.f === obj2.b.f);
// false
```

### 递归实现深拷贝
```javascript
function clone( o ) {
    var temp = {};
    for( var k in o ) {
        if( typeof o[ k ] == 'object' ){
             temp[ k ] = clone( o[ k ] );
        } else {
             temp[ k ] = o[ k ];
        }
    }
    return temp;
}
```

结语：这是本人在开发过程中实际总结的相关方法，也是比较有效的方法，如果大家有更好的方法，也欢迎留言。