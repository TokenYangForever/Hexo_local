title: 使用Javascript做算法题（三）Largest-Number
tags:
  - JavaScript
  - 算法
categories:
  - leetcode算法题
author: Token
date: 2017-09-15 13:36:00
---

---
#### 题目描述
* 给出一串非负整数组成的数组，求用这些数字组成的一个最大数字。举个例子：给出数组[3, 30, 34, 5, 9]，那么组成的最大数字就是9534330。注：返回结果数字一定相当大，所以用字符串表示返回的结果。[题目地址](https://leetcode.com/problems/largest-number/description/)
#### 解题思路
* 这道题解法的关键在于**排序数组**，需要将数组以生成最大数的顺序排列。我刚开始想到的是，首位数字大的数字就往前放，但是要考虑多位数他可能后面几位比较小的情况。最后发现解题思路很简单：**设计一个排序函数：比如两个数字为m和n，就比较mn和nm哪个数字更大，然后在数组调用sort()的时候传入它。**
#### 具体代码
```
/**
 * @param {number[]} nums
 * @return {string}
 */
var largestNumber = function(nums) {
    let len = nums.length
    if (len === 0) {
        return ""
    }
    if (len === 1) {
        return nums[0].toString()
    }
    let result = ''
    let sortBy = function (a, b) {
        let ab = a.toString() + b 
        let ba = b.toString() + a
        return Number(ba) - Number(ab)
    }
    nums.sort(sortBy)
    for (let i =0;i<len;i++){
        result += nums[i]
    }
    while (result.startsWith ('0') && result.length > 1) {
        result = result.substring(1)
    }
    return result
};
```
