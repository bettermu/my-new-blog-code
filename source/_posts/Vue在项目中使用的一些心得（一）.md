---
title: Vue在项目中使用的一些心得（一）
date: 2019-03-17 21:44:55
tags:
  - vue
categories:
  - vue  
---



1、elementUI组件Message和MessageBox的引入，需要按以下方式引入：

```js
Message.install = function (Vue, options) {
  Vue.prototype.$message = Message
}

Vue.use(Message)
MessageBox.install = function (Vue, options) {
  Vue.prototype.$confirm = MessageBox
}

Vue.use(MessageBox)
```

2、如果项目是多页构建，页面之间的跳转，js的方式只能采用：

```js
window.location.href = "./page.html"
```

不过如果page.html下面还有对应的router页面，可以这么来写：
```js
window.location.href = "./page.html#/sonPage"
```

不过我们知道，上面的location.href写法是相对路径的，但是我们可以采用绝对路径去写，比方说，我们打包给后端，后端文件的根目录是"/page/home",我们可以这样去写：

```js
//config.js
export const path = process.env.NODE_ENV === 'production' ? '/page/home':''

//所需跳转的页面
import {path} from './config.js'
window.location.href = path + "/page.html#/sonPage"
```

这样我们打包好后，发给后端，就能够正常显示了，而且跳转正常



