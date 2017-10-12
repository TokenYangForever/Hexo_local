title: Object属性特性详解
tags:
  - JavaScript
categories:
  - JavaScript
author: Token
date: 2017-09-15 13:36:00
---

---
### 前言
　Object是javascript中最原始的对象，也是理解面向对象十分重要的一环。最近看vue源码时，发现object这方面的东西非常多，就在这里深入了解一下相关知识。本篇主要介绍object对象的一些方法，以及属性的特性相关概念。首先介绍对象的属性，属性类型其实分为两种：**数据属性**和**访问器属性**，我们平时接触到的一般都是数据属性，下面依次介绍一下。
### 数据属性
　数据属性包含一个数据值的位置，可以进行数据的读写。然后数据属性有四个描述其行为的**特性**，这个特性可以理解为属性的属性。
1.[[configurable]]：表示是否能够修改属性的特性，是否能够使用delete删除属性进而重新定义。
2.[[Enumberable]]：表示是否可以被枚举，此特性为true时，对应的属性在for-in循环中才返回。
3.[[Writable]]：是否可写，此特性为true时，才可以定义修改对应属性的数据值
4.[[Value]]：即属性的数据值。
上面几个特性，前三个默认值都是true，Value默认值undefined。随便定义一个变量及其属性，然后调用Object.getOwnPropertyDescriptors()即可显示其属性，以及属性的特性。
![示例.jpg](http://upload-images.jianshu.io/upload_images/6383319-efd2d41b73e40ba6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 访问器属性
　首先要明白的是访问器属性不包含数据值，包含一对getter和setter函数。读取访问器时调用getter，写入访问器时调用setter。这两个函数默认值都是undefined，只有使用Object.defineProperty()方法对其进行定义。接着介绍一下object的一些方法。
#### Object.create() 
　参数传入一个原型对象，使用指定的原型对象和其属性创建了一个新的对象。
#### Object.assign()
　将所有**可枚举**的属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。第一个参数是目标对象，如果有相同键值的属性，源对象的属性会覆盖掉木雕对象属性， 方法只会拷贝源对象**自身的**并且**可枚举**的属性到目标对象身上。
#### Object.defineProperty() Object.defineProperties()
　Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。Object.defineProperty(obj, prop, descriptor)，第一个参数是目标对象，第二个参数是指定属性，第三个参数是描述符对象指定特性。在指定描述符时，只能选择**数据描述符**和**存取描述符**的其中一种。Object.defineProperties(obj, props)也是用于为一个对象定义属性，但是可以同时指定多个属性。
#### Object.keys()
　**Object.keys()** 方法会返回一个由一个给定对象的**自身可枚举属性**组成的数组，数组中属性名的排列顺序和使用for...in 循环遍历该对象时返回的顺序一致 （两者的主要区别是 一个 for-in 循环还会枚举其原型链上的属性）。
#### Object.freeze() Object.seal()
　这两个方法是用于限制对象修改权限的，分别有对应的Object.isFrozen()和Object.isSealed()方法判断是否是冻结(密封)对象，两者唯一的区别是，密封对象仍然可以修改已有属性value的值。
　Object.freeze() 方法可以冻结一个对象，冻结指的是不能向这个对象添加新的属性，不能修改其已有属性的值，不能删除已有属性，以及不能修改该对象已有属性的特性。也就是说，这个对象**永远是不可变的**。该方法返回被冻结的对象。
　Object.seal() 方法可以让一个对象密封，并返回被密封后的对象。密封对象是指那些不能添加新的属性，不能删除已有属性，以及不能修改已有属性特性，但可能可以**修改已有属性的值**的对象。
### 结语
　暂时总结就这么多，上面的内容大多是从js高程上看的，在线api[查的这里](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object)，后面有遗漏或不准确的地方再进行修改。最后说一点，上述的object的方法很多都不支持ie8，这也是为什么vue不支持ie8，但如果是在现代高版本的浏览器进行开发时就不用顾虑了。
