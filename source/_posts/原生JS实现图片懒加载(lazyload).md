title: 原生JS实现图片懒加载(lazyload)
tags:
  - JavaScript
  - HTML
categories:
  - JavaScript
author: Token
date: 2017-09-15 13:36:00
---

---
## 前言
 图片懒加载也是比较常见的一种性能优化的方法，最近在用vue做一个新闻列表的客户端时也用到了，这里就简单介绍下实现原理和部分代码。
*****
## 实现原理
 加载页面的时候，图片一直都是流量大头，针对图片的性能方法也挺多的比如base64、雪碧图等；懒加载也是其中一种，主要原理是将非首屏的图片src设为一个默认值，然后监听窗口滚动，当图片出现在视窗中时再给他赋予真实的图片地址，这样可以保证首屏的加载速度然后按需加载图片。

![示例.png](http://upload-images.jianshu.io/upload_images/6383319-ba10ad7a53809f30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 具体代码
 首先在渲染时，图片引用默认图片，然后把真实地址放在data-*属性上面。
<code><image src='./../assets/default.png' :data-src='item.allPics' class='lazyloadimg'></code></br>
 然后是监听滚动，直接用window.onscroll就可以了，但是要注意一点的是类似于window的scroll和resize，还有mousemove这类触发很频繁的事件，最好用节流(throttle)或防抖函数(debounce)来控制一下触发频率。underscore和lodash里面都有封装这两个方法，这里先不多做介绍了。<br>
 接着要判断图片是否出现在了视窗里面，主要是三个高度：1，当前body从顶部滚动了多少距离。2，视窗的高度。3，当前图片距离顶部的距离。offsetTop相关属性可以[参考这里](http://www.jianshu.com/p/135731ec13f1)，具体代码如下：
<code>
window.onscroll =_.throttle(this.watchscroll, 200);
watchscroll () { 
      var bodyScrollHeight =  document.body.scrollTop;// body滚动高度
      var windowHeight = window.innerHeight;// 视窗高度
      var imgs = document.getElementsByClassName('lazyloadimg');
      for (var i =0; i < imgs.length; i++) {
        var imgHeight = imgs[i].offsetTop;// 图片距离顶部高度  
        if (imgHeight  < windowHeight  + bodyScrollHeight) {
           imgs[i].src = imgs[i].getAttribute('data-src');
           img[i].className = img[i].className.replace('lazyloadimg','')
        }
      }
    }
</code>
*****
## 结语
 大概内容就这么多了，下次可能会补充一下防抖节流源码的实现。最后再补充两个常见的滚动判断：<br>1.页面滚动离开首屏(这时可显示回到顶部的按钮):<code>document.body.scrollTop > window.innerHeight</code>
2.页面滚动到底部了(这时可去调接口获取更多内容)：<code>window.scrollY + window.innerHeight > document.body.offsetHeight</code>
