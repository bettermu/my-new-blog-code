---
title: JS模拟bind方法实现
date: 2018-06-01 22:38:27
tags:
  - 原生js
categories:
  - js
---


## bind

在日常的js开发中，我们常常需要跟this指针打交道。那么我们常用的一个绑定this指针到当前上下文中的bind方法，不管是在es5中的常规绑定方式，还是在es6中如React中动态绑定this指针的方法：
```javascript
class XXX extends React.Component {
  constructor(){
    this.XXfunction=this.XXfunction.bind(this)
  }

  XXfunction(){
    ....
  }

  render(){
    return(
      <div onClick={this.XXfunction}></div>
    )
  }
}
```
如上代码所示，只用在constructor初始化函数内使用bind方法绑定this，才能在标签中使用this.XXFunction的写法   
****
那么这个bind到底是如何实现this的绑定的呢？我们又怎么去模拟实现一个自己的bind函数？首先我们来看下，bind函数的定义：
****  
bind() 方法会创建一个新函数。当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。(来自于 MDN )  
****
由此，我们能够得出bind函数的两个特点  

* 返回一个函数
* 可以传入参数


## 返回函数的实现

从第一个特点开始，举例：
```js
var foo={
  val:1
}
function haha(){
  console.log(this.val);
}

//返回了一个函数
var bindFoo=haha.bind(foo);

bindFoo(); // 1
```
下面呢，为了实现上面的结果，第一版的代码如下，将使用apply的特性来实现，关于apply和call的使用方法，这里不再赘述：
```js
  Function.prototype.bind2=function(context){
    
    //缓存this，保证返回的函数内能正常访问其所在的外部上下文
    var self=this;

    //返回的函数
    return function(){

      //利用apply来改变this指向
      self.apply(context)
    }
  }


```

## 传参的模拟实现
接下来，看第二点，可以传入参数。这个该怎么实现呢，我在bind的时候，是否可以传参呢？我在执行bind返回函数的时候，是否可以传参呢？举个例子：
```js
var foo={
  val:1
}

function haha(name,age){
  console.log(this.val);
  console.log(name);
  console.log(age);
}

var bindFoo=haha.bind(foo,'better');

bindFoo('20');
// 1
// better
// 20
```

函数需要传入name和age两个参数，竟然还可以在bind的时候，只传一个name，在执行返回的函数的时候，再传入另外一个参数age，其实现方法，类似于曾探的javascript设计模式里所说过的那种高阶函数，函数的柯里化，利用arguments的特性：
```js
//第二版
Function.prototype.bind2=function(context){
  var self=this;
  //获取bind2函数从第二个参数到最后一个参数
  var args1=Array.prototype.slice.call(arguments,1);

  return function(){
    //获取返回函数时传入的参数
    var args2=Array.prototype.slice.call(arguments)
    self.apply(context,args1.concat(args2))
  }
}
```

## 构造函数效果的实现
完成了这两点，最难的部分在于，bind还有一个特点：
****
一个绑定函数也能使用new操作符创建对象：这种行为就像把原函数当成构造器。提供的 this 值被忽略，同时调用时的参数被提供给模拟函数。
****
也就是说，当bind返回的函数作为构造函数的时候，bind时指定的this值会失效，但传入的参数依然生效，举个例子：
```js
var value=2;
var foo={
  val:1
}
function haha(name,age){
    this.habit = 'shopping';
    console.log(this.value);
    console.log(name);
    console.log(age);
}

haha.prototype.friend='huhu';

var bindFoo=haha.bind(foo,'better');

var obj=new bindFoo('20');
// undefined
// better
// 20

console.log(obj.habit); //shopping
console.log(obj.friend); // huhu
```

注意：尽管在全局和 foo 中都声明了 val 值，最后依然返回了 undefind，说明绑定的 this 失效了，如果大家了解 new 的模拟实现，就会知道这个时候的 this 已经指向了 obj。   
所以，我们可以通过修改返回的函数的原型来实现：
```js
//第三版
Function.prototype.bind2=function(){
  var self=this;
  var args1=Array.prototype.slice.call(arguments,1);

  var fbound=function(){
    var args2=Array.prototype.slice.call(arguments);
    // 当作为构造函数时，this 指向实例，self 指向绑定函数，因为下面一句 `fbound.prototype = this.prototype;`，已经修改了 fbound.prototype 为 绑定函数的 prototype，此时结果为 true，当结果为 true 的时候，this 指向实例。
    // 当作为普通函数时，this 指向 window，self 指向绑定函数，此时结果为 false，当结果为 false 的时候，this 指向绑定的 context。
    self.apply(this instanceof self?this:context,args1.concat(args2))
  }
  //修改返回函数的prototype为绑定函数的prototype，实例就可以继承函数的原型中的值
  fbound.prototype=this.prototype;
  return fbound;
}
```

## 构造函数效果的优化实现
但是在这个写法中，我们直接将 fbound.prototype = this.prototype，我们直接修改 fbound.prototype 的时候，也会直接修改函数的 prototype。这个时候，我们可以通过一个空函数来进行中转：
```js
//第四版
Function.prototype.bind2=function(){
  var self=this;
  var args1=Array.prototype.slice.call(arguments,1);

  var fNOP=function(){};

  var fbound=function(){
    var args2=Array.prototype.slice.call(arguments);
    self.apply(this instanceof self?this:context,args1.concat(args2))
  }
  
  fNOP.prototype=this.prototype;
  fbound.prototype=new fNOP();
  return fbound;
}
```

## 还有个小问题
调用 bind 的不是函数咋办？需要进行抛出错误
```js
if (typeof this !== "function") {
  throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
}
```

## 最终代码
所以最终代码是：
```js
Function.prototype.bind2=function(){
  
  if (typeof this !== "function") {
    throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
  }
  var self=this;
  var args1=Array.prototype.slice.call(arguments,1);

  var fNOP=function(){};

  var fbound=function(){
    
    self.apply(this instanceof self?this:context,args1.concat(Array.prototype.slice.call(arguments)))
  }
  
  fNOP.prototype=this.prototype;
  fbound.prototype=new fNOP();
  return fbound;
}
```



