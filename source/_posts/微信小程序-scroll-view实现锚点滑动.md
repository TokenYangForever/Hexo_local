title: 微信小程序   scroll-view实现锚点滑动
author: TokenYangForever
tags:
  - JavaScript
  - 微信小程序
categories:
  - JavaScript
  - ''
date: 2017-11-10 15:41:00
---
### 前言
* 最近开始做小程序，通读一遍文档再上手并不算难，但不得不说小程序里还是有一些坑。这里说一下如何实现页面锚点跳转，一个城市列表的效果示意图如下：
![city.gif](http://upload-images.jianshu.io/upload_images/6383319-104bb55559f4a26b.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 因为在微信小程序的环境中不能想在浏览器里设置标签，或者操作dom滚动，传统做法就行不通了，一切都得按小程序的文档来。
* 一开始我们的做法是使用**boundingClientRect()方法获取每个锚点的坐标**，然后再用wx.pageScrollTo()方法滑动过去。结果发现行不通，因为boundingClientRect方法返回的每个点的坐标会随着屏幕滑动而变化，**最后还是选择[scroll-view(可滚动视图区域)](https://mp.weixin.qq.com/debug/wxadoc/dev/component/scroll-view.html)
组件来实现锚点效果。**
### 具体实现
* 具体API就不赘述了，可以去看官方文档，这里讲几个需要注意的地方，下面是一个示意的scroll-view组件代码，上面的几个属性是必须的：
```
<scroll-view scroll-y style="height: 200px;" bindscroll="scroll" scroll-into-view="{{toView}}" >
```
* scroll-into-view：这个绑定了一个属性，它的值应该是页面元素的id，设置它的值就可以跳转到ID对应的元素那里了。
* scroll-y：添加这个属性标明是竖向滑动的，对应的scroll-x则表示横向滑动，**竖向滑动时scroll-view必须设置一个固定的height**
* bindscroll：监听滑动，传给他一个事件，滑动时执行该事件
* 文档上给的属性特别多，暂时只需要上述几个就可实现我们想要的效果。实现原理也很简单，内容部分，每个英文简写的view设置一个id，然后在导航list那里点击时，就把scroll-into-view的值设置成点击的那个id即可实现跳转。
* 再说一下scroll-view的高度问题，这个一定要做适配的固定高度，不然在不同屏幕大小的手机上的显示效果有差异。
### 几点优化
* 到这里功能基本都实现了，但后面还发现一些问题：如果要隐藏scroll-view的滚动条，需要设置css样式:::-webkit-scrollbar
```
::-webkit-scrollbar {
	width: 0;
	height: 0;
	color: transparent;
}
```
* 还有就是点了一个锚点实现了跳转，这个时候你滚动页面再点之前点的锚点，页面就不会再跳转了，这个时候就需要监听滚动事件，滚动时将scroll-into-view的值清空。
