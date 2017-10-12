title: 使用Javascript做算法题（二）Unique-Paths
tags:
  - JavaScript
  - 算法
categories:
  - JavaScript
author: Token
date: 2017-09-15 13:36:00
---

---
#### 题目描述
* 在一个 m*n 个格子大小的地图上，一个机器人位于左上角，它的终点在右下角，机器人的前进路线只能向右或向下走，求一共有多少种不同的路线走到终点。注：m和n不会超过100。[题目地址](https://leetcode.com/problems/unique-paths/description/)

![一个3*7的示例](http://upload-images.jianshu.io/upload_images/6383319-9b976ff4ae8da7eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 解题思路
* 看完这个题目，我首先联想到了另一个类似的题，就是上n阶台阶，可以跨一步可以跨两步，求一共有多少种方法。解题方法就是去考虑最后一步，最后要嘛从**n-1阶**，要嘛从**n-2阶**跨到终点。**假设走到n-1阶有m种不同方案，走到n-2阶有n种不同方案，那么走到n阶就有m+n种不同方案。**
* 有点扯远了，回到这个题目。首先想到的是**以二维数组来表示这个地图的坐标**，机器人的起点在(0,0)终点在(m,n)，机器人最后一步肯定从(m-1,n)或(m,n-1)走，那走到(m,n)的路线总数就是走到(m-1,n)的路线总数加上走到(m,n-1)的路线总数。至此，解题思路也就很明显了，**首先确定机器人走到最上面的一行和最左边的一列上的任意点，路线数只能为1。**然后再根据这些点的路线数，依次往里推，最后得出(m,n)点的路线数。
#### 具体代码
```
/**
 * @param {number} m
 * @param {number} n
 * @return {number}
 */
var uniquePaths = function(m, n) { 
    if (m == 1 || n == 1) {
        return 1
    }
    var arr = []
    arr[0] = new Array(n).fill(1)
    for (var i = 1; i < m; i++) {
        arr[i] = new Array(n)
        arr[i][0] = 1
        for (var j = 1; j < n; j++) {
            arr[i][j] = arr[i][j-1] + arr[i-1][j]
        }
    }
    return arr[m-1][n-1]
};
```
