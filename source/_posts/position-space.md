---
title: 获取元素位置信息和所占空间大小(via:js&jquery)
date: 2017-02-24 18:29
tags: [JS,JQuery,CSS]
---
> 工作中有一个很常见的需求，hover或者click某元素后，在该元素旁边出现弹框，主要就是获取该元素的位置坐标以及元素所占区块的大小。最近工作中就遇到了，发现js和jquery的实现方法有很大的区别，这里总结一下，备忘。



1. **获取元素的相对位置信息**
    * 原生js方法
        ```javascript
            var x = obj.offsetLeft,
                y = obj.offsetTop;
        ```
    * jquery方法
        ```javascript
            var _offset = $obj.offset(),
                x = _offset.left,
                y = _offset.top;
        ```
    这两个方法看起来相似，其实有很大的不同.
 <!-- more -->
    1. 原生js获取位置的参考信息是：父辈元素中的定位元素，即非static(默认值)的元素。而juqery方法始终返回相对于文档的偏移。
    2. 当父元素有滚动条的时候，两者的情况也是不同的。js会把滚动的距离也算在相应的偏移量中，而jquery的值不受滚动条的影响。
    综合以上两点不同可以看出：当父元素不会出现滚动条且父辈元素的定位属性都统一(即参考点要相同)的时候，可以用原生方法获取位置信息，弹框用相对定位(相对于上面提到的参考点).jquery则相对简单很多，因为他的计算始终相对于文档，所以弹框用fixed定位即可。
2. **获取元素所占区块的大小**
    * 原生js方法
        ```javascript
            // 方法1
            var _width = obj.style.width,
                _height = obj.style.height;
            // 方法2
            var _width = obj.offsetWidth,
                _height = obj.offsetHeight;
        ```
        其中方法一基本可以忽略，因为他只能获取到内敛样式。当然可以代替他的方法也有，情参考[通过原生js获取元素的样式](https://erichui0906.github.io/2017/02/24/getStyle/)
    * jquery方法
        ```js
            // 获取高度的方法类似
            var _width1 = $obj.width(),
                _width2 = $obj.innerWidth(),
                _width3 = $obj.outerWidth(),
                _width4 = $obj.outerWidth(true);
        ```
        有多重方法可以获取元素的大小，下面分别介绍异同。由盒模型可以知道，元素所占空间大小 = width + padding + border + margin; 所以可以得到以下对应关系：
        ```js
            _width1 = width;
            _width2 = width + padding;
            _width3 = width + padding + border;
            _width4 = width + padding + border + marign;
        ```
        原生js方法2获取的宽度_width相当于jquery的方放获取到的_width3;
***

根据以上获取到的信息就可以定位弹框的位置了：
```js
    $popup.css({ // 此处把弹框定位在元素的右侧居中位置
        left: x + _width,
        top: y + _height/2
    })
```