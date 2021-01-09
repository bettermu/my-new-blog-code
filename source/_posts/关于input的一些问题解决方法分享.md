---
title: 关于input的一些问题解决方法分享
date: 2018-05-22 09:38:54
tags:
  - input
  - bug
categories:
  - js
---

input框是我们接受来自用户的数据常用标签，在前端开发中，相信每个人都会用到这个标签，特别是在移动端H5页面的开发中，也会经常遇到一些问题，下面我将根据自己的开发过程中遇到的那些关于input的问题，在这里向大家集中分享一下，所对应的的解决方案。



## 1、移动端底部input被弹出的键盘遮挡
input输入框是通过position:fixed一直放在页面底部，当点击input进行输入的时候，就会出现如下的图片情况
![](https://github.com/bettermu/blog-picture-store/blob/master/20180522/input%E8%A7%A3%E5%86%B3.jpg?raw=true)

其实这个问题是我去年年后在一个微信H5项目里遇到的，当时公司的测试机，ios9的系统，出现的这个bug，但在新的ios系统里，貌似已经解决了这个bug，但是为了向下兼容以及防止其他机型也出现这样的问题，解决方法如下：

```
  Element.scrollIntoView()
```

顾名思义，该方法是让当前的元素滚动到浏览器窗口的可视区域内

```javascript
document.querySelector('#inputId').scrollIntoView();  //只要在input的点击事件，或者获取焦点的事件中，加入这个api就好了
 ```


## 2、控制input显隐密码
这个就很简单了，只需要改变input的type属性就可以了：
```javascript
  //点击函数，获取dom，判断更改属性。
    show(){
        let input=document.getElementById("inputId");  
        if(input.type=="password"){ 
          input.type='text';
        }else{
          input.type='password';
        } 
    }
```


## 3、textarea多行回车换行，显示的时候换行设置：
这个问题是在今年5月做的上海的一个智慧旅游亭项目的时候，后台编辑相关酒店景点的时候，有用到textarea，原本在textarea里设置的多行回车换行，结果在前台显示的页面里，没有换行，查找了相关资料，解决方法如下：  
css属性：white-space
```css
  ele {
    white-space:pre-wrap;
  }
```
只要在显示内容的地方，将该属性设置为white-space:pre-line或者white-space:pre-wrap,多行文本就可以换行了。  

上述内容就是我遇到的一些input问题的解决方式以及跟input相关的一些东西，如果有什么错误，欢迎指正！