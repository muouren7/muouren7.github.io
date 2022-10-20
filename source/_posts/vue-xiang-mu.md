---
title: 'Vue项目页面上方有空白'
date: 2022-10-14 16:11:07
tags: [vue]
published: true
hideInList: false
feature: 
isTop: false
---


# Vue项目页面上方有空白（解决方案）
## 问题
新建的vue项目，里面的每个页面中，上方都有几像素的空白
![解决前](https://raw.githubusercontent.com/muouren7/PIC/master/img/202209150953701.png)
## 解决
在vue项目下的App.vue中，样式表的顶部外边距默认是60像素,会将页面的body元素与html隔开,将样式表的顶部外边距改位0px即可
```css
#app {
  margin-top: 60px;
}
```
![解决后](https://raw.githubusercontent.com/muouren7/PIC/master/img/202209150956088.png)
改了之后就没有空白了。