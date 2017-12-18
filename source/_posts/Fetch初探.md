title: Fetch初探
author: TokenYangForever
tags:
  - Html
categories:
  - Html
date: 2017-11-22 13:34:00
---
#### 前言
* fetch这个东西，之前也看到过有人介绍，但毕竟还是实验性阶段的东西，最近在看react-native的文档，讲到网络的地方，文档上是推荐地使用fetch，想必框架本身也对其有所封装。这里就先自己学习一下fetch相关的基础知识。
#### 基本概念
* [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) 是一个现代的概念, 等同于[XMLHttpRequest](https://developer.mozilla.org/enUS/docs/Web/API/XMLHttpRequest)。它提供了许多与XMLHttpRequest相同的功能，但被设计成更具可扩展性和高效性。下图显示了fetch在浏览器中的支持情况，可以看到在主流浏览器的支持性还是不错的，实在不支持fetch的浏览器(比如IE)上面也可以使用相应的polyfill库。
![fetch在浏览器中的支持情况.png](http://upload-images.jianshu.io/upload_images/6383319-9575a5a9c01ac1fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 具体用法
* 使用fetch首先需要具备promise的相关知识，还涉及到一个[Response](https://developer.mozilla.org/zh-CN/docs/Web/API/Response)对象，直接来看下代码，这是一段调用json格式数据接口的示例代码：
```
fetch('http://101.200.35.148:8081/todo/getTodo')
  .then(response => {
    response.json().then(
        // 这里的result就是最终的接口数据了
        result => {
          console.log(result)
        }
    )
  })
```
* 在支持fetch的浏览器中，fetch方法是global的可以直接调用。上述代码是一个get请求(默认方法是get)，fetch执行后会返回一个promise对象，这个promise对象resolve之后得到一个response对象([Response](https://developer.mozilla.org/zh-CN/docs/Web/API/Response)的实例)。
* **response对象**相当于整个流程中的一个中间状态，它自带一个json方法用于解析从接口得到的数据，还有一个属性bodyUsed标识是否已经被解析，response.json()执行后也是返回一个promise对象，正确执行后then()这里得到的result对象就是最终的接口数据了。执行完json方法后，response对象的bodyUsed会变成true，表示这个response对象是已经被读取过了的，这时候如果再次调用response的json方法就会报错。
#### POST请求
* 接着来看下fetch如何处理post请求，fetch方法可以接收第二个参数来设置发送请求的方法、header、请求参数等，下面是一个post请求的示例。
```
fetch("/post/api/", {
    method: "POST",
    body: "A=参数A&B=参数B",
    headers: new Headers()
})
```
* 其中body就表示请求参数，可根据具体情况自行调整数据结构;method设定请求方法，默认为get大小写皆可，headers用于设置发送请求的header。fetch在请求post的时候如果没有成功，则会尝试重新以get方法发送请求。
#### 使用async/await
* 因为多了一层response，这里的promise回调代码结构就略显复杂，所以用fetch时使用async/await代码就会精简许多，可读性也会大大提高，比如下面这样：
```
  async componentDidMount () {
    try {
      let res = await fetch('http://101.200.35.148:8081/todo/getTodo')
      let {result} = await res.json()
      this.setState({
        result
      })
    } catch(e) {
      console.log(e)
    }
  }
```
#### 额外内容
* fetch除了用于post/get调取接口以外还可以请求图片，下面是一段请求图片，然后将图片显示在dom上的代码：
```
var myImage = document.querySelector('img');

fetch('flowers.jpg')
.then(function(response) {
  return response.blob();
})
.then(function(myBlob) {
  var objectURL = URL.createObjectURL(myBlob);
  myImage.src = objectURL;
});
```
* 可以看到代码第一步还是获取response对象，之后调用了response.blob()来得到一个blob对象，这里又涉及到了另一个新特性[Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)，继续调用URL.createObjectURL可将blob对象转换为一个临时链接供img标签使用。Blob相关暂不多做介绍，这里只是为了说明一下fetch还有这一种用法。
* 参考： https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch
