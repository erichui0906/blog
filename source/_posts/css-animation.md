---
title: 常见web动画总结
date: 2016-09-06 00:09:50
tags: [css3,js]
---
>目前，web动画主要有两大类：CSS3动画和javascript动画。

#### 一 CSS3动画

现在页面中的动画一般都是css3动画，主要通过transition和animation实现。注意这里没有transform，css3的2D和3D转换是不能单独产生动画的。

**transition动画**
transition动画的实现方式，是为元素设置需要产生动画效果的属性，当他们变化时就会有相应的过度(transition)效果。transition主要有四个属性：transition-property,transition-duration,transition-timing-function,transition-delay;其中前两个(动画名称和动画之行时间是必须的)。

```js
.demo {
	transition: left .5s ease 1s; // demo元素left属性改变的时候会产生动画
}
```
**animation动画**
animation是关键帧动画，他是预先设置多个节点，每个节点有不同的状态属性。他对元素的控制更加精细化，所以可以做出更为复杂的动画。
要产生animation动画，首先要指定关键帧规则，@keyframes+animation-name。然后在绑定到选择器上。animation有八个属性：

```js
.demo {
	animation-name: animationName;  // 动画名称 @keyframes
	animation-duration: 1s;         // 动画之行时间(ms)
	animation-time-function: ease;  // 速度曲线
	animation-delay: 1s;			// 动画延迟执行的时间
	animation-iteration-count: 2;   // 动画被播放的次数，默认1，infinite无限播放
	animation-direction: alternate; // 动画下一周期是否反向播放，normal正常播放，alternate反向播放
	animation-play-state: paused;   // 规定动画之行与否，可用js操作该属性，播放和暂停动画
	animation-fill-mode: forwards;  // 动画播放前后，动画效果是否可见
}
```
这里的最后一个属性，可以很好的解决动画过程中闪现的问题，其中默认值none不改变行为，forwards动画结束后，保持最后一个关键帧的壮体啊，backwards在动画执行之前应用第一个关键帧的状态，both使用以上两个特性。

#### 二 js动画

js动画是动态的控制并改变元素的各种属性值以形成动画效果。
最简单的就是多个兄弟元素采用定位的方式，放在同一个地方，js控制他们的显隐性。还可以通过一些其他的类库实现，比如强大的JQuery动画。还可以通过HTML5种的canvas来生成canvas动画。

#### 三 总结
>大部分简单的页面动画都可以通过css3动画实现，js动画往往用于需要结合用户操作而产生的动画效果。在兼容性方面css3动画要比JQuery类库动画差一点，但css3动画的执行效率比他高一点。