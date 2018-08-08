---
title: 一次搞懂javascript原型链和继承
date: 2018-06-28 16:01:29
comment: true
tags: [前端,javascript]
categories: [前端,技术]
---
javascript原型链是个很容易被新手前端忽略的重要概念，在初级编程工作中绝大部分人可能会觉的原型链似乎没啥用、因此非常容易被忽略。我自己刚入行那会儿就被面试官问懵过（知道这个东西、却说不清楚）。

### 1、原型链
直入主题，原型链是js中实现对象继承的主要方法。基本思想是利用原型让一个对象继承另一个对象的属性和方法。
首先对象的构造函数其实就是普通的函数，只是使用方式不一样（构造函数一般用和new操作符一起使用），无论什么时候，只要创建了一个函数，就会根据一组特定的规则为这个函数创建一个prototype属性，而这个属性就指向函数的原型对象。
而且在默认情况下，所有原型对象都会获得一个constructor（构造函数）属性，这个属性就是一个指向构造函数的指针。如下面代码所示：

```bash
function Person() {
    this.name = "Logan";
    this.age = "29";
};
Person.prototype.constructor === Person;
// true
```

从上面的代码可以看出，构造函数的prototype的constructor属性是严格相等于Person构造函数的

此外，构造函数的每个实例都有一个指向构造函数的原型对象的内部指针，这个指针叫做[[prototype]]，在浏览器中这个指针就是对象实例的\_\_proto\_\_属性。
接下来关键的来了，假如我们让原型对象等于另一个类型的实例，结果会怎么样呢？我们先来举个例子：

```bash
functoin SuperType (){
    this.type = "supper";
    this.color = ['red', 'blue', 'green'];
};
SuperType.prototype.getSuperValue = function () {
    return this.type;
};
 
function SubType = function() {
    this.subType = "sub";
};
// 将SuperType的实例赋值给SubType的原型对象
SubType.prototype = new SuperType();
 
SubType.prototype.getSubValue = function(){
    return this.subType;
};
 
var instance = new SubType();
console.log(instance.getSuperValue());    //"supper"
```
上面的代码通过把SuperType的实例赋值给SubType的原型对象而使SubType继承了SuperType的属性和方法

显然，现在SubType的原型对象是SuperType的实例，因此SubType的原型对象包含了一个指向SuperType的原型对象的一个指针，而SuperType的原型对象中包含了一个指向SuperType自己的constructor属性。那么现在的指向关系就是：
instance的\_\_proto\_\_指向SubType.prototype === SuperType的实例（三个等号是相等的意思）
SuperType实例的\_\_proto\_\_指向SuperType.prototype。这之间的关系如下图所示：

<img src="prototype1.png" alt="">

接下来，如果SuperType的原型对象又是另一个类型的实例，那么上述关系依然成立，如此层层递进，就构成了实例与原型的链条。而这就是所谓原型链的基本概念。

理解了原型链，那么我们接下来了解一下实现继承的另外5种方式：

首先，通过原型链来实现继承是有缺点的。
第一，子类型（就是上面的SubType）的原型对象就是超类型（SuperType）的实例，因此SubType的所有实例都会继承SuperType这个实例的属性，而问题就出在这里。上面的代码中SuperType的构造函数有个color属性，我们通过下面的代码来揭示问题：

```bash
//以下代码接着上面的代码
instance.color.push("black");
var instance2 =  new SubType();
console.log(instance2.color);
// ['red', 'blue', 'green', 'black']
```

### 2、借用构造函数
借用构造函数继承的出现正式为了解决原型链继承所存在的问题。我们先通过代码来看借用构造函数的基本模式：

```bash
function SuperType = function (name) {
    this.name = name;
};
 
funciton SubType = function () {
    //继承了SuperType，同时还继承了参数
    SuperType.call(this, "Logan");
    this.age = 29;
}; 
var instance = new SubType();
console.log(instance.name);    // "Logan"
console.log(instance.age);    // 29
```

从上面的代码可以看出，借用构造函数就是在子类的构造函数中通过call()或apply()执行父类构造函数（就像执行一个普通函数一样）。这样由于继承的所有属性都在子类的构造函数中，所以子类的实例在继承了所有父类属性的同时又不会互相影响。

但是，很容易看出，借用构造函数同样是有缺点的：
首先，需要继承的所有属性都必须在父类的构造函数重定义（包括函数/方法），这样每次创建一个子类实例都要创建相同功能但是彼此独立的函数，因此函数复用就无从谈起了。

#### 为了解决这些问题后续又出现了：组合继承，原型式继承，寄生式继承
但是这些方法都存在一些这样或那样的问题。

#### 最完美的实现继承的方式是：寄生组合式继承
寄生组合式继承是通过借用构造函数方式来继承父类的属性，通过将父类原型对象的副本赋值给子类原型对象的方式来继承父类的方法：

```bash
function SuperType (name) {
    this.name = name;
    this.colors = ["red", "blue", "green"];
};
SuperType.prototype.sayName = funciton () {
    alert(this.name);
};
 
functon SubType = function (name, age) {
    // 在子类构造函数内部调用父类构造函数
    SuperType.call(this, name);
 
    this.age = age;
};
// 创建父类原型对象的副本
var prototype = Object(SuperType.prototype);
prototype.constructor = SubType;    // 把prototype的constructor指向子类构造函数
SubType.prototype = prototype;
```

通过上面的代码我们可以看到，寄生组合式继承不但通过在子类构造函数内部运行父类构造函数而实现了属性的继承，还通过将父类的原型对象副本赋值给子类原型对象的方式继承了父类的方法。这样子类继承的属性之间不会互相影响，同时又达到了函数复用的效果。

好了，以上就是这次分享的基础内容。中间由于继承方式的种类太多太繁杂而跳过了三种方法，如果想要了解请查阅__《javascript高级程序设计》168-171页__。

如有错误还望指正，谢谢！