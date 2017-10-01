---
title: js数组去重方法分析与总结
date: 2017-08-08 11:35:53
tags: [js]
---
>数组去重经常被人拿来说事，虽然在工作中不常用，但他能够很好的考察js基础知识掌握的深度和广度，下面从js的不同阶段总结一下去重的方法。

### ES3阶段

该阶段主要通过循环遍历数组从而达到去重的目的

* 多次循环去掉重复元素

```js
// 以下所有方法默认都那拿该数组进行测试
var array = [1, 1, '1', '1', null, null, undefined, undefined, new String('1'), new String('1'), /a/, /a/, NaN, NaN,{},{},[],[],{name: 'eric',sex: 'male'},{sex: 'male',name: 'eric'}];
function unique_es3_on2 (arr) {
    var len = arr.length,
        i = 0,
        j,
        flag,
        ret = [];
    for (; i < len ; i++) {
      flag = true;
      for (j = i+1 ; j < len ; j++) {
        if (arr[i] === arr[j]) {
          flag = false;
          break;
        }
      }
      if(flag) {
        ret.push(arr[i]);
      }
    }
    return ret;
}
unique_es3_on2(array)
```
结果如下图：![](http://orrzkg1ur.bkt.clouddn.com/es3_on2.png)

可见除了NaN没有去掉，其他效果都还挺好。原因就是NaN===NaN的结果是false。还有就是使用嵌套的循环，时间复杂度高，性能不是很好。

<!-- more -->

* 借助新的对象单次循环去掉重复元素

```js
function unique_es3_on(arr) {
    var obj = {},
        i,
        len = arr.length,
        ret = [];
    for(i = 0; i < len ; i++) {
      if(!obj[arr[i]]) {
        obj[arr[i]] = true;
        ret.push(arr[i]);
      }
    }
    return ret;
}
```
结果如下图：![](http://orrzkg1ur.bkt.clouddn.com/es3_on.png)

虽然时间复杂度不高了，但是效果并不好。因为对象的属性是字符串，所以会把数组所有元素默认转化为字符串，就会产生以下问题：

1. 数值1和字符串'1'以及包装类型new String('1')，转化为字符串以后是相同的会被去掉。
2. 对象转化为字符串以后会被误判，[]==>'',{}==>'[object Object]',还有就是形式相同，内存地址不同的对象会被去除。

为了解决类型转换以后出现的问题，可以用typeof操作符转一下：

```js
function unique_es3_on(arr) {
    var obj = {},
        i,
        len = arr.length,
        str,
        ret = [];
    for(i = 0; i < len ; i++) {
      str = typeof arr[i] + arr[i];
      if(!obj[str]) {
        obj[str] = true;
        ret.push(arr[i]);
      }
    }
    return ret;
}
```
结果如图：![](http://orrzkg1ur.bkt.clouddn.com/es3_on_typeof.png)

可以看到类型转换的问题基本解决，但对象部分基本都被去除了，因为他们和字符串相加时还是会发生转化，解决的方案是把上面的str换成``str = typeof arr[i] + JSON.stringify(arr[i])``，相加之前先简单序列化一下。

结果如图： ![](http://orrzkg1ur.bkt.clouddn.com/es3_on_json.png)

>从以上可以看出该阶段的各种方法或多或少的都有一些问题，该去除的没去掉，比如NaN。不该去的给去掉了，比如，形式相同但内存地址不同的对象(是否应该去掉全看你怎么定义)。

### ES5阶段

```js
function unique_es5(arr) {
    return arr.filter(function(ele,index,array) {
      return array.indexOf(ele) == index;
    })
}
```

结果如图：![](http://orrzkg1ur.bkt.clouddn.com/es5.png)

>可以看到除了NaN，其他表现都是正常的。其中indexOf对于NaN总是返回-1，所以导致误判。

### ES6阶段

```js
let unique_includes = (arr) => {
    let newArr = [];
    arr.forEach(function(item){
        if(!newArr.includes(item)){
            newArr.push(item);
        }
    });
    return newArr;
}
```

结果如图： ![](http://orrzkg1ur.bkt.clouddn.com/es6_includes.png)

可以看到结果是符合预期的，es6中数组的扩展方法includes解决了用indexOf的弊端(不够直观，结果还要和索引进行比较。对NaN的误判)。

```js
let unique_set = (arr) => {
    return [...new Set(arr)];
}
```

结果和includes方法一样，此处利用es6新增数据结构set的特性，达到去重的目的。

```js
let unique_map = (arr) => {
    let ret = [],
        m = new Map();
    for(val of arr) {
      if(!m.get(val)) {
        m.set(val , true);
        ret.push(val);
      }
    }
    return ret;
}
```
结果和includes一样： ![](http://orrzkg1ur.bkt.clouddn.com/es6_includes.png)

此处利用es6新增数据结构map的特性，之前的键值对集合（js对象），只能用字符串当作健，map这种数据结构打破了这一限制，各种类型的值都可以当作健，而且map的健是跟内存地址绑定的，只要内存地址不同就认为是不同的健，解决了之前形式相同而内存地址不同被去掉的问题。对于简单数据类型，只要严格相等就认为是相同的健，特例NaN也认为是相同的健。所以就解决了之前的两个大难题。

### 总结
>虽然只是一个简单的去重问题，但这一路实践下来，可以看到js越来越强大，功能也越来越完善，同时也越来越优雅。现在再听到有人说，js只是处理简单表单验证的玩具车语言之类的云云，我想我也会忍不住在他耳边怼一句：那他妈是从前。






