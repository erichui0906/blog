---
title: javascript中各种继承方式的优缺点
date: 2017-06-25 10:23:00
tags: [javascript,es6]
---

>javascript中实现继承的方式有很多种，一般都是通过原型链和构造函数来实现。下面对各种实现方式进行分析，总结各自的优缺点。

#### 一 原型继承

```js
let Super = functioin(name) {
	this.name = name;
	this.setName = (newName) => {
		this.name = name;
	};
	this.getName = () => {
		return this.name;
	}
}

let Sub = function(sex) {
	this.sex = sex;
}
Sub.prototype = new Super('eric');  //通过改变原型对象实现继承
let sub1 = new Sub('male')
	 sub2 = new Sub('female');

sub1.setName('ada');
// 这里必须通过setName方法来修改继承来的name属性。
// 如果通过sub1.name== 'ada',就打不到目的，因为此时sub1对象上没有name属性，
// 这样等于为该对象添加了新的属性，而不是修改继承而来的name属性。
console.log(sub2.name); // ada,可见此sub2的name也会被修改掉
console.log(sub1.getName === sub2.getName) // true,复用了方法
	 
```
**优点：**父类的方法(getName)得到了复用。

**缺点：**同理父类的属性(name)也是复用，即子类实例没有自己的属性。

#### 二 构造函数实现继承
<!-- more -->
```js
let Super = function(name) {
	this.name = name;
	this.getName = () => {
		return this.name;
	}
}
let Sub = function(sex,name) {
	Super.call(this,name); // 调用父类方法为子类实例添加属性
	this.sex = sex;
}

let sub1 = new Sub('male','eric'),
	 sub2 = new Sub('female','eric');
sub1.name = 'ada';
console.log(sub2.name); // eric,实例的属性没有相互影响

console.log(sub1.getName === sub2.getName); // false,可见方法没有复用

```
**优点：**子类的每个实例都有自己的属性（name），不会相互影响。

**缺点：**但是继承父类方法的时候就不需要这种特性，没有实现父类方法的复用。

#### 三 组合式继承

```js
let Super = function(name) {
	this.name = name;
}
Super.prototype = {
	constructor: Super, // 保持构造函数和原型对象的完整性
	getName() {
		return this.name;
	}
}
let Sub = function(sex) {
	Super.call(this,'eric'); //继承父类属性
	this.sex = sex;
}
Sub.prototype = new Super('eric'); //继承父类方法
Sub.prototype.constructor = Sub;
let sub1 = new Sub('male'),
    sub2 = new Sub('female');
// 可以按上述两种方法验证，复用了父类的方法，实例没有复用，达到目的
```
**优点：**继承了上述两种方式的优点，摒弃了缺点，复用了方法，子类又有各自的属性。

**缺点：**因为父类构造函数被执行了两次，子类的原型对象(Sub.prototype)中也有一份父类的实例属性，而且这些属性会被子类实例(sub1,sub2)的属性覆盖掉，也存在内存浪费。

#### 四 寄生组合式继承

```js
let Super = function(name) {
	this.name = name;
}
Super.prototype = {
	constructor: Super,
	getName() {
		return this.name;
	}
}
let Sub = function(sex,name) {
	Super.call(this,name);
	this.sex = sex;
}
// 组合继承的缺点就是在继承父类方法的时候调用了父类构造函数，从而造成内存浪费，
// 现在只要解决了这个问题就完美了。那在复用父类方法的时候，
// 使用Object.create方法也可以达到目的，没有调用父类构造函数，问题解决。
Sub.prototype = Object.create(Super.prototype);
Sub.prototype.constructor = Sub;
```

#### 五 es6中的class

```js
class Super() {
	constructor(props) {
		this.name = props.name || 'eric';
	}
	setName(name) {
		this.name = name;
	}
	getName() {
		return this.name;
	}
}
class Sub extends Super {
	constructor(props) {
		super(props); // 创建实例，继承父类属性和方法
		this.sex = props.sex || 'male';
	}
}
let sub1 = new Sub({
	name: 'eric',
	sex: 'male'
})
let sub2 = new Sub({
	name: 'eric',
	sex: 'female'
})

sub1.setName('ada');
console.log(sub1.getName(),sub2.getName()) // ada,eric,属性没复用，各自实例都有自己的属性。
console.log(sub1.getName === sub2.getName) // true; 复用了父类的方法
console.log(Sub.prototype.sex) // undefined
// 子类原型对象上没有父类构造函数中赋值的属性，不是组合式继承
```
**由以上结果可以看到es6中的class只不过是一种语法糖，通过上面的验证得知符合寄生组合继承的特点，但这只是猜测，class具体都做了哪些操作还不是很清楚，后面有时间，对class做一下研究。**