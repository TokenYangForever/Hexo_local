title: vue源码解析之--核心部分core-util(二)
tags:
  - vue
  - 框架源码
  - JavaScript
categories: []
author: Token
date: 2017-09-15 13:36:00
---

---
### 前言
　在vue源码目录中有个core文件夹，其核心功能比如组件、数据绑定、虚拟dom等等都在里面。core下面又分为了components、global-api、instance、obsever、util、vdom六个部分，这次还是先以util开头，里面也封装了一些工具型的函数并且引用了外层share里面的工具函数。 
#### debug.js error.js
　debug.js里面主要封装的是用于开发阶段的调试相关的方法，比如一些提示警告。error.js里面只封装了一个handleError方法，用于处理错误，可在config里设置专门的errorHandler函数，没有的时候就只是一个warn警告。debug.js里面有一个repeat方法，将传入字符串重复N次。
<code>  var repeat = function (str, n) {
    var res = '';
    while (n) {
      if (n % 2 === 1) { res += str; }
      if (n > 1) { str += str; }
      n >>= 1;
    }
    return res
  };</code>
这样写的时间复杂度大概是二分之一N，lodash里也有相似的方法，不过是用Math.round(n/2)来代替n>>=1，n>>=1就是把N转换成二进制，然后右移一位再首位补0.
#### env.js
　env.js主要是一些环境相关的东西，待会儿重点说的是nextTick方法。
<code>var inBrowser = typeof window !== 'undefined'</code>
是否在浏览器中，只要判断window对象是否存在即可。
<code>var UA = inBrowser && window.navigator.userAgent.toLowerCase();
</code>
浏览器中通过userAgent，判断浏览器的版本内核。
<code>function isNative (Ctor) {
  return typeof Ctor === 'function' && /native code/.test(Ctor.toString())
}
</code>
isNative 判断某些方法功能是否原生支持的，比如浏览器支持symbol的话，symbol.tostring就会返回function Symbol() { [native code] }，再用正则表达式判断是否包含native code即可。
　下面讲一下nextTick方法，这个方法也比较常用，可以在确定数据改变视图改变后再执行某些动作。代码首先定义一个自执行匿名函数的作用域，然后定义了一个nextTickHandler函数用于执行完成异步后需要执行的函数。
<code>  function nextTickHandler () {
    pending = false
    const copies = callbacks.slice(0)
    callbacks.length = 0
    for (let i = 0; i < copies.length; i++) {
      copies[i]()
    }
  }</code>
pending是一个标示符，用于确保timerFunc方法不会被重复调用。接下来是根据浏览器支持情况来定义timeFunc方法：
<code>if (typeof Promise !== 'undefined' && isNative(Promise)) {
    var p = Promise.resolve()
    var logError = err => { console.error(err) }
    timerFunc = () => {
      p.then(nextTickHandler).catch(logError)
      if (isIOS) setTimeout(noop)
    }
  }</code>
如果浏览器支持promise,就用promise.then来定义timerFunc,promise的具体用法这里也不多讲了,然后我看源码上的注释是说:promise.then执行的时间可能不稳定,当他被推入一个微任务队列中时,添加一个空的setTimeout可以强制刷新微任务队列(IOS系统中).
<code>else if (typeof MutationObserver !== 'undefined' && (
    isNative(MutationObserver) ||
    // PhantomJS and iOS 7.x
    MutationObserver.toString() === '[object MutationObserverConstructor]'
  )) {
    // use MutationObserver where native Promise is not available,
    // e.g. PhantomJS IE11, iOS7, Android 4.4
    var counter = 1
    var observer = new MutationObserver(nextTickHandler)
    var textNode = document.createTextNode(String(counter))
    observer.observe(textNode, {
      characterData: true
    })
    timerFunc = () => {
      counter = (counter + 1) % 2
      textNode.data = String(counter)
    }
  }</code>
当promise不被支持，但浏览器支持MutationObserver时。MutationObserver是html5的一个新特性，用于监测dom节点变化。初始化一个MutationObserver的实例observer ，传入的nextTickHandler是作为监测到发生变化后执行的回调函数，然后这里新建一个文本节点，再使用observer .observe()方法将文本节点绑定监听。最后定义的timerFunc就是改变文本节点的值，从而触发回调，这个回调是在文本节点重新渲染后执行的。当以上两个方法都不支持时，就只好用setTImeout来定义timerFunc了。
<code>return function queueNextTick (cb?: Function, ctx?: Object) {
    let _resolve
    callbacks.push(() => {
      if (cb) {
        try {
          cb.call(ctx)
        } catch (e) {
          handleError(e, ctx, 'nextTick')
        }
      } else if (_resolve) {
        _resolve(ctx)
      }
    })
    if (!pending) {
      pending = true
      timerFunc()
    }
    if (!cb && typeof Promise !== 'undefined') {
      return new Promise((resolve, reject) => {
        _resolve = resolve
      })
    }
  }</code>
最后返回的函数其实就是nextTick自身，传入两个参数，一个是异步回调执行的函数，第二个是一个对象作为执行的环境。将传入的函数以 cb.call(ctx)的形式添加到callback中，然后再执行timerFunc。在global.api中，将这里定义的nextTick放在vue的原型上了。
env.js在最后定义了_Set，如果支持set那么就直接用set了，不支持的话，定义了一个_set类，并为其添加has()、add()、clear()方法。其实set的用法也比较简单，浏览器不支持的话自己重新定义一下也不复杂。
