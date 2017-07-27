---
title: 原生js获取元素的样式信息
date: 2017-02-24 18:27
tags: [JSON,CSS]
---
> 工作中经常会需要获取DOM元素的样式，之前都是通过jquery的css()方法，现在总结一下通过原生js获取元素样式的方法。


* **obj.style**
    ```js
        var _width = obj.style.width;
    ```
    该方法基本可以忽略，因为他只能获取到内敛样式。而现在很少会写内联样式。当然他有个好处是可读可写，所以可以据此设置元素的样式。
* **window.getComputedStyle**
    ```js
        // 语法如下： window.getComputedStyle('元素','伪元素');
        var _css = window.getComputedStyle(obj,null); // 不涉及伪元素的可以传null
        var _width = _css.getPropertyValue('width'); // _css.width
    ```
    该方法对样式可读不可写，返回一个描述元素最终样式的对象。获取某一个样式的值，可以像访问对象属性那样访问，但建议使用getPropertyValue(propertyName)方法获取(此处属性名不支持Camel-Case,所以要用类似'background-color')。可惜的是该方法在IE低版本(<IE8)是不支持的，所以IE下可以用下面的方法。
* **obj.currentStyle**
    ```js
        var _css = obj.currentStyle;
        var _width = _css.getAttribute('width'); // _css.width
    ```
    该属性是IE自己的一个属性，也是可读不可写的。需要注意的是此处获取具体样式值的时候，getAttribute(propertyName),其中属性名必须是驼峰式命名。