title: apply与call性能分析
tags:
  - JavaScript
categories:
  - JavaScript
date: 2017-09-15 13:53:21
---

---
#### apply call 基本概念
* 稍有一些javascript基础的人应该对这两个函数都不会陌生，这里简单过一下基本概念。这两个方法一般都是用来调用一个函数时改变其内部this指向，换句话说：**劫持一个对象的方法，继承另外一个对象的属性并调用**。语法上两者唯一的不同就是传参形式：
* Function.apply(obj,args)方法能接收两个参数：
obj：这个对象将代替Function类里this对象
args：这个是数组，它将作为参数传给Function（args-->arguments）
* Function.call(obj,[param1[,param2[,…[,paramN]]]]) call方法接收多个参数，比如你调用函数时传入三个参数就得这样写： fun.call(obj,arg1,arg2,arg3)，在不确定传入参数有多少个时用call来执行就很麻烦。
* 以前我的理解就是这两个函数区别就是上面这些了，最近我才知道，他们执行起来的速度差异还挺大的。
#### apply与call性能对比
* 这里有个网站：[https://jsperf.com/call-apply-segu]()，执行测试用例，得出的结果是以Ops/sec（每秒操作数）为单位。根据操作系统和浏览器内核版本等不同，最后的结果也不一样，但总体来说可以得到的结论是：**call方法永远比apply方法执行速度要快**。下面贴两张运算结果的截图：
![Safari浏览器MacOs系统](http://upload-images.jianshu.io/upload_images/6383319-cf3c9edc35aafe40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Chrome浏览器window系统](http://upload-images.jianshu.io/upload_images/6383319-94db063de6db0239.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 初步结论是速度和**浏览器内核**有关，但apply肯定是要比call运行速度更慢的，在chrome上甚至可以看见，结果居然差了一个位数。
* 所以在一些框架中，**为了追求更快的执行速度就是舍弃掉apply方法或者用call来模拟apply方法。**比如我们现在框架上就是这样做的：
```
switch (args.length) {
        case 0:
          g = gen.call(ctx);
          break;
        case 1:
          g = gen.call(ctx, args[0]);
          break;
        case 2:
          g = gen.call(ctx, args[0], args[1]);
          break;
        default:
          g = gen.apply(ctx, args);
      }
```
* 这样根据参数的长度，switch/case判断来执行不同的call语句，实在没有case的时候再执行apply方法。虽然这样写看起来代码很累赘，直接用apply就只需要一句话的事，但是如果要追求极致性能还是需要这样做（在angular2的源码中也有类似的部分，其中写了20个case判断参数长度）。
#### 结语
* 那是不是说以后都不要用apply方法了呢？当然不是啦~在项目中某些文件，比如启动程序时执行的，用到了apply方法，但理论上这里只执行一次，那这个时候换成call 方法带来的性能提升基本也可以忽略不计了。
* ES7中有一个关于绑定this的提案：并排的两个冒号（::）组成的函数绑定运算符，它的左边是一个对象，右边是一个函数，通过这个运算符将左边对象作为this绑定到右边函数上面去。这种写法就不用纠结是用call还是apply了，但现在浏览器都不支持，需要用babel转义。
```
foo::bar(...arguments);
// 等同于：
bar.apply(foo, arguments);
```
* 在不确定参数的情况下，如果不考虑性能，代码层面用apply来写会简洁很多。不过你能确认参数长度的时候，最好都用call方法而不用apply。
