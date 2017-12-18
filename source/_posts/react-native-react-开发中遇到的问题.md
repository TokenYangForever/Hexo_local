title: react-native/react 开发中遇到的问题
author: TokenYangForever
tags:
  - react-native
  - react
categories:
  - react
  - react-native
date: 2017-12-06 16:26:00
---
#### 前言
* 最近对react-native产生些兴趣，准备学一学，写写demo练练手，然而之前对react页不是很熟悉，所以一边学一边写code，一边看react-native的文档一边撘环境。中途还是遇到很多问题，写篇文章mark下。
#### react-native搭建开发环境时遇到的问题
* 搭建Android开发环境：
1. java版本不能装9.0+的，路径格式不同，Android-studio无法识别路径。
2. Android SDK 要装25.0+的，不然会提示版本过低无法继续，然而教程(甚至是fb官网上)还是写的装23.0
3. 模拟器，先在Android-studio打开Android模拟器之后，再在命令行执行react-native run-android命令
4. 执行react-native run-*命令时，启动的packager server需要使用8081端口，确保这个端口没有占用！
#### RN中写样式
* 本来平时我css就写得少，RN中这种变种的css写起来更蛋疼。。。。[React-Native 样式指南](https://github.com/doyoe/react-native-stylesheet-guide)
* RN中的样式相当于css的缩减版，命名方式要用驼峰法，然后大部分属性都能和css中对应。布局：也有相对定位和绝对定位，也支持flex。**官方推荐flex**，可以解决大多数的布局情况。
* 建议使用StyleSheet.create()生成样式的类名，也可以直接在组件上写"内联"样式。
* RN中元素的**宽高是没有单位的**，直接用数字表示即可，也可以用flex来实现弹性宽高。
* 另外就是要注意，不同的组件可以设置的样式是不同的，比如遇到无法设置宽高的组件就只能在它外层包个view，然后设置这个view的宽高。
#### RN中调试
* 打开调试菜单：iOS模拟器中：Command⌘ + D 快捷键；Android模拟器对应的则是Command⌘ + M
* Enable Live Reload：开启自动刷新
* Enable Hot Reload：开启热加载
* 访问控制台日志:在终端中运行：react-native log-ios或者log-android
* 在开发者菜单中选择"Debug JS Remotely"选项，即可以开始在Chrome中调试JavaScript代码。点击这个选项的同时会自动打开调试页面 http://localhost:8081/debugger-ui.
* 注:Chrome中并不能直接看到App的用户界面，而只能提供console的输出，以及在sources项中断点调试js脚本。

#### RN中的网络通信
* 文档上介绍的是使用fetch，我也专门去了解了一下下，算是ajax的进化版，结合了一些html5的新特性。RN中也支持websocket。
* 默认情况下，iOS会阻止所有非https的请求。如果你请求的接口是http协议，那么首先需要添加一个App Transport Security的例外，详细可参考[这篇帖子](https://segmentfault.com/a/1190000002933776)。

#### RN中的数据存储
* 在浏览器环境，我们可以使用cookie、localstorage或者sessionstorage来实现数据缓存，在RN中则不能使用以上的三种方式。
* [AsyncStorage](http://reactnative.cn/docs/0.50/asyncstorage.html#content)是一个简单的、异步的、持久化的Key-Value存储系统，它对于App来说是全局性的。它用来代替LocalStorage。

#### react相关
* react事件需要手动绑定this，或者使用箭头函数来声明
```
this.handleClick = this.handleClick.bind(this);
// 或者
onClick={(e) => this.handleClick(e)}
handleClick = () => {...}
```
* react的组件必须以大写字母命名: 小写字母的标签会被认为是html的标签而不会被编译。一般表达式声明react的组件也要用大写字母开头：
```
const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
```