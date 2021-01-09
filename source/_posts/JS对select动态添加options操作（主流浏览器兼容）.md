---
title: JS对select动态添加options操作（主流浏览器兼容）
date: 2018-03-23 09:50:16
tags:
  - js
categories:
  - js
---

　　之前项目中，遇到一个表单提交的页面，里面有多级下拉框联动的复杂逻辑，因此当时在做的过程中也是学到了不少容易出现问题的地方，下面就整理下当时遇到的一些关于下拉框的操作，并指出其中的一些注意点和坑：

有如下的下拉框： 
```html
<select id="ddlResourceType" onchange="getvalue(this)"> 
</select> 
```
* 动态删除select中的所有options：
```js
document.getElementById("ddlResourceType").options.length=0;
```
* 动态删除select中的某一项option：
```js
document.getElementById("ddlResourceType").options.remove(index); 
```
* 动态添加select中的项option: 
```js
document.getElementById("ddlResourceType").options.add(new Option(text,value)); 
```
* 下面要说下在取值方面，自己遇到的一个坑，很隐藏的坑，大家都知道，如何获取选中的某一项option的值，当初在用chrome调试的时候，打印出了原生select对象，找到了selectedOptions这个选中项的数组属性，正常情况下，单选下拉框的该属性只有一个值，默认可以使用selectedOptions[0]这样的方式去获取，是的，我当时就是这样去弄的，结果项目上线后大概将近半个月的时候，运营那边报告说，客户在IE浏览器下，下拉框没有数据显示，我当时就想，下拉框动态获取的数据肯定是取数据的时候，出现错误了，然后逐一去排查，发现报错的原因是ie下面select原生对象里，是没有selectedOptions这样的属性的，那么只能另辟蹊径来解决了，我仔细找了下剩余的一些属性，发现  找到了 selctedIndex这样的一个好属性，一看字面意思就明白了，就是选中项的index值啊，这就好办了啊，直接用options[selectedIndex]去访问不就好了么，到此，问题顺利解决，取值的时候，应该这样做，才能保证兼容性：
```js
function getvalue(obj) 
    { 
        var m=obj.options[obj.selectedIndex].value 
        alert(m);//获取value 
        var n=obj.options[obj.selectedIndex].text 
        alert(n);//获取文本 
    }
```
* 检测是否有选中
```js
if (objSelect.selectedIndex > - 1 ) { 
// 说明选中 
} else { 
// 说明没有选中 
} 
```
* 删除被选中的项 
```js
objSelect.options[objSelect.selectedIndex] = null ; 
```
* 增加项
```js
objSelect.options[objSelect.length] = new Option( " 你好 " , " hello " ); 
```
* 修改所选择中的项
```js
objSelect.options[objSelect.selectedIndex] = new Option( " 你好 " , " hello " );
```
* 得到所选择项的文本
```js
objSelect.options[objSelect.selectedIndex].text; 
```
* 得到所选择项的值
```js
objSelect.options[objSelect.selectedIndex].value;
```


结语：纸上得来终觉浅，绝知此事要躬行。必须要去尝试，在实践中，才能获得解决问题的最有效的途径，这也是让记忆深刻的唯一方式，共勉。
