title: 使用Javascript做算法题（一）Maximum Subarray
author: TokenYangForever
tags:
  - JavaScript
  - 算法
categories:
  - JavaScript
date: 2017-09-14 11:35:00
---
#### 前言
* 最近开始在leetcode上接触算法题，印象中的算法题大都是用cpp或者java解的，而这个网站支持javascript解题提交。因为大学也没学过算法，数据结构也只是稍微了解，所以解题目标也是选简单经典的题。下面开始讲解这道题--53.Maximum Subarray.[题目地址](https://leetcode.com/problems/maximum-subarray/description/)
#### 题目描述
* 给出一个由数字组成(含负数)的数组，找出它的一个**每项相加之和最大**的子数组，返回这个最大和数字。举例：给出一个数组``[-2,1,-3,4,-1,2,1,-5,4]``, 每项和最大的子数组为``[4,-1,2,1]``,返回最大和数字6。
#### 解题思路
* 首先抛出一个名词[动态规划算法](https://baike.baidu.com/item/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92%E7%AE%97%E6%B3%95/15742703?fr=aladdin)，概念理解起来较为抽象，我个人的理解是求最优解的题基本都可以用动态规划作为解题思路。
* 首先可以确定解题的复杂度，这里其实只要遍历一次数组即可，**时间复杂度为O(n)**，而需要**维护的状态变量**有两个，**全局最大值和局部最大值**。就是随着从第一项开始往后推进，你需要确定从第一项到第n项时已经出现的元素组成的**最大和**，这个状态变量就是**全局最大值**；而局部最大值是遍历到第n项时，**包含第n项的情况下的最大值**。全局最大值是由局部最大值推导出的，下面是具体代码：
```
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) {
    if(!nums.length)
        return 0
    let local = nums[0]
    let global = nums[0]
    for (let i = 1; i < nums.length; i++) {
        local = Math.max(nums[i], local + nums[i]) // 包含第n项的情况下的最大值
        global = Math.max(local, global)   // 全局最大值
    }
    return global
};
```