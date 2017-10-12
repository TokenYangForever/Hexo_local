title: vue服务端渲染(SSR)初探
tags:
  - JavaScript
  - 服务端渲染
  - vue
  - node
categories: []
author: Token
date: 2017-09-15 13:36:00
---

---
### 前言
首先来讲一下服务端渲染，直白的说就是在服务端拿数据进行解析渲染，**直接生成html片段返回给前端**。具体用法也有很多种比如： 
* 传统的服务端模板引擎渲染整个页面
* 服务渲染生成htmll代码块, 前端 AJAX 获取然后js动态添加
### 服务端渲染的优劣
　首先是**seo**问题，前端动态渲染的内容是不能被抓取到的，而使用服务端渲染就可以解决这个问题。还有就是**首屏加载**过慢这种问题，比如在SPA中，打开首页需要初始加载很多资源，这时考虑在首屏使用服务端渲染，也是一种折中的优化方案。但是使用SSR时，势必会增加服务器的压力，还有可能会需要前后端同构，使用同样的模板引擎，这似乎与前后端分离的观点又是矛盾的。废话就说到这里，下面来看一下vue框架中的服务器渲染。
### vue-server-renderer
　vue-server-renderer就是vue中处理服务端加载的一个模块了，官方文档：[https://ssr.vuejs.org/en/](https://ssr.vuejs.org/en/)，暂时没有中文版的，我也只是稍微看了一些，然后写了一个简单的demo。首先新建一个test.js文件，并用npm安装依赖express、vue、vue-server-renderer。引入vue-server-renderer之后，然后新建一个temp.html作为渲染的基本模板，用createRenderer方法新建一个render实例，这里我传入temp.html作为renderer的template的参数，在后面渲染时就会以这个temp.html作为基础模板。
```
const renderer = require('vue-server-renderer').createRenderer({
	template: require('fs').readFileSync('./temp.html', 'utf-8')
})
```
temp.html:
```
<!DOCTYPE html><br><html lang="en"><br><head><title>{{title}}</title></head>
  <body>
    <!--vue-ssr-outlet-->
  </body>
</html>
```
　接下来随便定义一些渲染用的数据，然后用express新建一个node服务器，再定义一个vue的实例。然后再调用renderer的renderToString方法来渲染生成html，渲染成功后返回给客户端。
```
const Vue = require('vue')
const server = require('express')()
const context = {
  title: 'hello'
}
const mocktitle = '我爱吃的水果'
const mockdata = ['香蕉', '苹果', '橘子']
server.get('*', (req, res) => {
  const app = new Vue({
    data: {
      url: req.url,
      data: mockdata,
      title: mocktitle
    },
    template: `<div>The visited URL is: {{ url }}
    <h3>{{title}}</h3>
    <p v-for='item in data'>{{item}}</p>
    </div>`
  })
  renderer.renderToString(app, context, (err, html) => {
    if (err) {
      res.status(500).end('Internal Server Error')
      return
    }
    res.end(html)
  })
})
server.listen(8080)
```
　注意这里渲染的数据有两种，mockdata是作为vue实例的data来渲染在实例模板中的，而context是作为基础模板的data来渲染temp.html的。可以看到在服务端用vue进行渲染的规则和前端渲染时一样，v-for、v-if等都可以正常使用。最后命令行输入node test.js，然后在浏览器打开http://localhost:8080 查看结果如下:
![示例.png](http://upload-images.jianshu.io/upload_images/6383319-877ce0eda788c464.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看到服务端直接返回了一个渲染完成的Doc，示例demo到此结束。
### 结语
　服务端渲染还是客户端渲染的问题，个人觉得还是要针对具体业务场景然后再做选择。
