title: 用react-native实现一个数独游戏(总结)
author: TokenYangForever
tags:
  - react
  - react-native
categories:
  - react
  - react-native
date: 2017-12-13 15:41:00
---
#### 前言
* 最近用react-native做了一个数独游戏的app，到今天基本功能已经全都完成并打包在真机上测试了，这里总结一下开发过程中的一些问题和实现的思路。
![image.png](http://upload-images.jianshu.io/upload_images/6383319-c4e4dbb2eaf0a4ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 环境和平台选择
* 项目上读取的接口是我自己服务器上部署的，用node实现的接口，链接mysql数据库，数独题目就是从这里读取得到。因为服务器上只有http接口，所以app开发时也就选择的android版本。
#### 功能及实现
* 因为只是一个简单的demo，做的功能也比较简单：新游戏/继续游戏/选择难度。页面有三个：首页/棋盘页面/关于。
* 页面导航使用的react-navigation，从首页进入棋盘页面时，会传参数过去表示是否是新游戏和游戏难度。
```
this.props.navigation.navigate('Main', {difficulty: text, newgame: true})
```
* 数独棋盘的生成，是从数据库得到数据，然后再渲染出来。一开始我是打算随机生成棋盘的，但是发现生成的棋盘可能会出现多解的情况，最后想想还是算了，在网上找了几个数独题目存在数据库里。
* 触发提交按钮后，会对棋盘进行验证，有空白没有填完则返回，然后再依次判断各个点是否满足数独条件，判断方法可参考LeetCode上的一道题：[Valid Sudoku](https://leetcode.com/problems/valid-sudoku/description/)，下面还是贴下判断结果的代码。
```
    let arr = this.state.arr
    let dp = {}
    let index = 0
    for (let a = 0; a < 3; a++) {
      for (let b = 0; b < 3; b++) {
        dp[`area${a}${b}`] = new Map()
      }
    }
    for (let i = 0; i < 9; i++) {
      dp[`x${i}`] = new Map()
      dp[`y${i}`] = new Map()
    }
    for (let i = 0; i < 9; i++) {
      for (let j = 0; j < 9; j++) {
        let temp = arr[index]
        if (temp !== '') {
          let xIndex = 'x' + j
          let yIndex = 'y' + i
          let areaIndex = 'area' + Math.floor(j/3) + Math.floor(i/3)
          if (dp[xIndex].has(temp) || dp[yIndex].has(temp) || dp[areaIndex].has(temp)) {
            return [false, '有空格填错了哦~', index]
          } else {
            dp[xIndex].set(temp, true)
            dp[yIndex].set(temp, true)
            dp[areaIndex].set(temp, true)
          }
        } else {
          return [false, '还有空格没填完哦~']
        }
        index++
      }
    }
    return [true]
```
* 数据存储还是使用的react-native提供的AsyncStorage，并对其进行一层包装。未完成的棋局缓存起来，继续游戏的时候再调用。
* 基本上就是这些了，以后有机会再补充。