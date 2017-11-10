title: javascript事件流、事件代理、target和currentTarget
author: TokenYangForever
tags:
  - JavaScript
categories:
  - JavaScript
date: 2017-10-31 09:54:00
---
### 前言
* 这篇讲的内容是非常基础的，关于javascript中的事件相关可能不太全面，只是围绕几个知识点。可能现在mvvm框架用多了也不用多去在意dom和event，但是原生的基础知识还是需要掌握的(万一以后面试问到了呢~~)
* Javascript与html之间的交互是通过**事件**来实现的。事件，就是文档或浏览器窗口发生的一些特定的交互瞬间，可以用侦听器(或处理程序)来预定事件，以便事件发生时执行相应的代码。
### 事件流
* **事件流**描述的是从页面接收到事件的顺序，一些历史发展的内容这里就不提了，直接讲"DOM2级事件"，事件流包括三个阶段：**事件捕获阶段、处于目标阶段和事件冒泡阶段**。最先发生的是事件捕获，然后是具体的实际目标收到事件，最后才是冒泡阶段，可以在这个阶段对事件做出响应。

![网上找的示意图.png](http://upload-images.jianshu.io/upload_images/6383319-f8a239bd4cb8b4ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### 添加事件监听
* 给元素添加事件监听的方法，可以直接在html上添加或者在javascript中指定相关的**事件处理程序**，但是需要注意，这种以'on'开头的事件属于'DOM0'级事件，重复定义时会覆盖掉原事件，而使用addEventListener则不会覆盖之前添加的。
```
<input id='btn' value='click me' onclick='alert(this.value)'>
```
* 还可以使用**addEventListener/removeEventListener**方法，所有dom节点都包含这方法，传入三个参数:事件名、处理事件的函数、标示在捕获还是在冒泡阶段调用回调函数的布尔值。
* addEventListener添加的事件处理函数只能用removeEventListener方法移除，传入参数与addEventListener相同，**意味着addEventListener添加的匿名函数无法移除，**所以在用addEventListener添加处理函数时不能使用匿名函数。环境不支持addEventListener的时候，还可以使用attachEvent/detachEvent，这里就不多做介绍了。
```
let btn = document.getElementById('btn')
let handler = function () {alert(this.id)}
btn.addEventListener('click', handler,false)
btn.removeEventListener('click', handler,false)
```
### 事件委托
* 事件委托是什么呢？最简单来说：**在父元素上添加可以处理子元素事件的事件处理函数**。利用了事件冒泡，指定一个事件处理函数，来处理同一种类型的多个事件。这里顺便一起说下target和currenttarget的区别，**target是触发事件的最具体的元素， currenttarget是绑定事件的元素(在函数中一般等于this)**。下面具体代码示例：
```
<div id="outter">
   <div id="inner"></div>
</div>

var outter = document.querySelector('#outter')
outter.addEventListener('click',function(e){
    console.log(e.target.id)  //inner
    console.log(e.currentTarget.id)  //outter
    console.log(this === e.currentTarget)  //true
},false);
```