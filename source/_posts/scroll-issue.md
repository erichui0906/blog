---
title: js中有关滑动问题的一些理解
date: 2016-09-05 17:29
tags: [JS,JQuery,CSS]
---
>在做导航栏的时候，肯定会用到点击滑动。还有就是滑动过程中，要根据滚动条的高度处理一些事情，比如改变导航栏样式。

#### 一 点击滑动(scrollTop)

先看一下下面的代码：

```javascript
$(window).animate({
   scrollTop: "1000px" 
})

$(document).animate({
   scrollTop: "1000px" 
})

$('html,body').animate({
   scrollTop: "1000px" 
})
```
上面三种方法哪种可行？不行的方法为什么行不通？
<!-- more -->
以前做的时候就知道第三种方法可以，另外两个不可以，但一直不知道为什么，现在又遇到了这个问题，所以不能再偷懒了，有必要追究一下为什么了。

查看了很多资料，发现可能是这个原因：

首先要了解animate()这个方法，他是jquery封装的一个方法，用于执行一个基于css属性的自定义动画。这句话很关键，他要基于css属性。看一下animate()方法的语法：

$(selector).animate(styles,speed,easing,callback) 或者$(selector).animate(styles,options)

我的理解是要想执行该自定义动画，selector选择器一定要有改属性才行，所以做了一下测试(简单的在chrome的控制台里敲了一下)，发现：

```javascript
window.scrollTop    // undefined
document.scrollTop  // undefined
```
window和document是全局的对象，他们的属性中并没有scrollTop，是不是就可以认为他们不具有这个css属性，所以就不能执行animate这个自定义动画。

但是document.body确实有scrollTop这个属性的：

```javascript
document.body.scrollTop   // 273
```
所以

```javascript
$(document.body).animate({
    scrollTop: 1200+"px"
})
```
这种写法是可以执行的。

下面再看一下html和body的情况：

```javascript
document.querySelector('html').scrollTop // 无论滑动到什么位置结果都输出0;
document.querySelector('body').scrollTop // 输出滑动的高度
```
后面用其他浏览器测试了一下，发现有一点也很重要：

$("html")和$("body")以及$(document.body)的区别

1）在chrome浏览器里面后面两个是可以的，html却不可以。

2）在IE以及firefox里面，html可以，后面两个却不可以。

所以最好的写法就是$("html,body")这样。

#### 二 滑动处理(scroll)

和上面一样，哪种方法可以，哪种方法不可以，为什么？？

```javascript
$(window).scroll(function() {
    console.log(1);
})

$(document).scroll(function() {
    console.log(2;
})

$("html,body").scroll(function() {
    console.log(3);
})
```
大家应该都知道前两种都是可以的，第三种是不可以的。重要的是，为什么？？？？和上面的情况好像刚好相反一样，是不是很奇怪。

和上面分析的方法一样，先看scroll()这个方法，它也是jqeury的一个方法，该方法为每个匹配元素的scroll事件绑定处理函数，它会触发scroll事件，该事件一般适用于window对象或其他可滚动元素(一般会出现滚动条)。

从上面的定义可以看出，可滚动元素即高度超出以后会出现滚动条的应该都可以才对，为什么第三种方法不可以？？

自己测试了才知道，当页面内容超过一屏的高度时，出现的滚动条是window或者说是document的，而不是html或body的，所以第三种方法才会无效，因为html或body没有设置高度，或者高度设置成了100%，所以没有出现滚动条。

如果强制给他们设置高度，并设置overflow-y:scroll的话，第三种方法也是会触发的。

**以上就是滑动过程中我遇到的一些问题,总结在此，以便查阅。**