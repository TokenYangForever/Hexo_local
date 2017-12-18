title: socket.io 实现在线聊天室
author: TokenYangForever
tags:
  - node
  - websocket
categories:
  - 后端
date: 2017-11-15 10:20:00
---
![效果图.gif](http://upload-images.jianshu.io/upload_images/6383319-ba462293ba9834bb.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 
#### websocket
* WebSocket协议是**基于TCP**的一种新的网络协议。它实现了**浏览器与服务器全双工(full-duplex)通信**——允许服务器主动发送信息给客户端。websocket链接建立后，服务端和客户端可通过连接通道自由通信。wx协议也有类似于https的wss协议，也是多了一层TLS协议。
* websocket具体详细的内容就不多做介绍了，因为我这次使用的socket.io框架，写起来的语法和原生websocket语法还是有很大差别的。
#### Socket.io简介
* socket.io可以看做是一个对websocket进行了封装的一个框架，目的也是为了实现客户端和服务端的全双工通信。socket.io的底层是engine.io，engine.io在不支持websocket的浏览器中，会使用ajax的长轮询实现效果。
* 具体过程，浏览器和服务端首先建立xhr链接，第一次握手后，服务端返回浏览器一个upgrades字段，告诉前端可以升级为websocket协议，socket.io客户端的框架会进行环境监测支持websocket的话，就会将xhr链接升级为websocket。建立起一个websocket连接后，服务器和浏览器之间还会定期的ping-pong来确定网络链接是否正常。
### 具体实现
* 再来看下具体实现的代码如何写，[socket.io官网](https://socket.io/)上面的文档和教程还是挺详细的，而我们要实现这个多人聊天室也只需要其中一些比较基本的api即可。
#### 服务端部署
* 服务端我用的node，直接用Npm安装socket.io，可以用socket.io直接搭建服务器，也可以在原有的express/koa上部署，我之前用的express，这里就是将socket.io添加到原有的express服务器上：
```
let app = new (require('express'))()
const server = app.listen(8081, () => {
  let peopleCounts = 0
  let io = require('socket.io')(server)
  io.on('connection', function (client) {
    // 这里的作用域是局部的，针对一个窗口发起的ws链接
    ++peopleCounts
    let uname = ''
    let uColor = require('./methods/util').getColor()

    client.on('disconnect', function (e) {
      --peopleCounts
      io.emit('sys message', {
        data: `系统消息: "${uname}"离开聊天室`,
        counts: peopleCounts
      })
    })

    require('./methods/getNickName')()
      .then(name => {
        uname = name
        io.emit('initUser', {
          name,
          color: uColor
        })
        io.emit('sys message', {
          data: `系统消息: "${name}"进入聊天室`,
          counts: peopleCounts
        })
      })
      .catch(e => { console.log(e) })

    client.on('chat message', function (data) {
      io.emit('message from server', data)
    })
  })
  console.log('Example app listening on port 8081!')
})
```
* 在express服务器搭建成功的回调函数里绑定socket.io，这里的io变量就是表示socket.io的服务端。
* .on方法监听事件，这些事件来自建立websocket连接时或者浏览器端主动触发。
* .emit触发事件，前端可以监听这些事件。比如一个客户端连接到服务端时，服务端会生成一个随机昵称(getNickName方法非必要)，然后返回给客户端。
#### 客户端部署
```
import io from 'socket.io-client'
export default {
  name: 'cheatRoom',
  data () {
    return {
      description: '',
      nickName: '',
      counts: 1,
      msg: [],
      color: ''
    }
  },
  created () {
    window.vm = this
    this.initSocket()
  },
  beforeDestroy () {
    // socket链接与vue生命周期绑定，vue销毁时手动断开socket链接
    this.socket.close()
  }
  methods: {
    initSocket () {
      const socket = io(this._config().preurl)
      socket.on('connect', () => {
        this.socket = socket
      })
      // 这里api语法类似于jQuery,支持once监听一次,off取消监听
      // [https://socket.io/docs/client-api/#socket-on-eventname-callback](https://socket.io/docs/client-api/#socket-on-eventname-callback)
      socket.once('initUser', ({name, color}) => {
        this.nickName = name
        this.color = color
      })
      socket.on('sys message', ({ data, counts }) => {
        this.counts = counts
        this.msg.push({
          description: data,
          sys: true
        })
      })
      socket.on('message from server', (item) => {
        item.sys = false
        this.msg.push(item)
      })
    },
    loadAction () {
      let { description, nickName, color } = this
      if (this.description.length === 0) {
        return
      }
      this.socket.emit('chat message', {
        description,
        nickName,
        color
      })
      this.description = ''
    }
```
* 前端需要用npm安装socket.io-client，不能直接使用原生的websocket语法建立连接。
* 初始化时this._config().preurl是写在配置里的一个url地址，同其他接口地址前缀一样，但是这里的url地址是http协议，也就是说一开始是把一个http协议地址传给的socket.io，然后socket.io在建立http链接之后才升级成的websocket链接。
* on方法，用来监听来自服务端的事件，once表示监听一次，还可以取消监听
* emit触发事件，让服务端响应。这里的loadAction就表示发送信息。

- - -
2017/11/24 更新
* 客户端我用的vue-cli搭建的项目，整个聊天室就是在一个vue实例中，socket连接与vue实例的生命周期绑定，created的时候建立连接，beforeDestroy的时候断开连接


####  结语
* 整体写法有点像观察者模式，服务端和客户端都是以监听/触发事件的形式完成通信，完全不同于传统的ajax交互方式，十分有趣。