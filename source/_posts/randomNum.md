---
title: 真的了解js生成随机数吗
date: 2017-02-15 16:53:33
tags:
    - js
---
>由`js`生成一切随机数的基础都是`Math.random()`，这个方法比较特别，生成的随机数落在的区间是[0,1)，进行一次操作的话，`js`只能生成一个类似于[n,m)这样，左闭右开的区间。所以当有一些特殊需求的时候，就势必要进行一些其它的操作，下面对各种需求进行简单的分析:




---

### 生成任意区间的整数

1. **全闭区间[n,m]**
    这种的最常见，大家都知道的那一长串公式：**`Math.floor(Math.random()*(m-n+1))+n;`** 就是生成这个全闭区间的方法。说到这个公式很多人都知道，但真正想明白的人估计很少。先生成一个[0,m-n+1)这样左闭右开的区间，然后用`Math.floor()`取到[0,m-n]之间内的任意整数（看明白这一步很关键），之后加上区间左端点变成[n,m]内的任意整数，达到目的。
<!-- more -->
    说到这个地方，有一点必须提一下，随便搜一下`js`生成随机数，有很多文章都会用`Math.ceil()`或`Math.round()`这两个方法，比如生成全闭的[n,m]区间内的任意整数，`Math.ceil(Math.random()*(m-n))+n;`或者`Math.round(Math.random()*(m-n))+n;`我感觉随机数，最重要的就是**随机**两个字，每个值取到的概率一定要相等，这一点对于一些特定的场合非常重要，比如抽奖（年会都有抽奖的吧）。`Math.ceil()`的毛病是x≈m>>n，x为除端点之外的数，区间足够大的话n几乎取不到，m和x的概率几乎相等，因为m这个点取不到所以概率相对来说小了一点。`Math.round()`的毛病是n≈m=x/2,原因和前面的差不多，不明白的可以自己画个坐标轴，很明了。

2. **全开区间(x,y)**
    其实只要记住上面的全闭区间，其它所有区间的开闭，都可以由其推到，过程如下：
    (x,y) ＝＝[x+1,y-1];也就是说n=x+1; m=y-1;将其代入上面的公式就可以得到：`Math.floor(Math.random()*(y-x-1))+x+1;`
    
3. **左闭右开[x,y)**
    同理，[x,y) ＝＝ [x,y-1];代入得到：`Math.floor(Math.random()*(y-x))+x;`

4. **左开右闭(x,y]**
    (x,y]＝＝[x+1,y];代入得到：`Math.floor(Math.random()*(y-x))+x+1;`
    
---

### 生成任意区间内的浮点数
>这种实际当中用到的比较少，但也挺有意思的。

* [n,m) 
    这种最简单，因为和random的特点保持一致。`Math.rondom()*(m-n)+n;`
* 因为random的这种特点，想要取到其它区间内的浮点数就比较困难了。需要借助一些判断才能才能满足要求。思想和上面去整数的一样。代码如下:

```javascript
        function fullClose(n,m) {  //[n,m]
            var result = Math.random()*(m+1-n)+n;
            while(result>m) {
                result = Math.random()*(m+1-n)+n;
            }
            return result;
        }
        function fullOpen(n,m) { // (n,m)
            var result = Math.random()*(m-n)+n;
            while(result == n) {
                result = Math.random()*(m-n)+n;
            }
            return result;
        }
        function leftOpen(n,m) { // (n,m]
            var result = Math.random()*(m-n+1)+n-1;
            while(result<n) {
                result = Math.random()*(m-n+1)+n-1;
            }
            return result;
        }
```
