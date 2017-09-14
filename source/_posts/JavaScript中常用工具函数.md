---
title: javascript常用工具函数总结(不定期补充)
date: 2017-09-12 16:18:02
tags: [JavaScript, 总结]
---
#### 前言
* 以下代码来自：自己写的、工作项目框架上用到的、其他框架源码上的、网上看到的。
* 主要是作为工具函数，服务于框架业务，自身不依赖于其他框架类库，部分使用到es6/es7的语法使用时要注意转码
* 虽然尽量在函数中做了错误情况的处理，仍有可能出现报错的情况(不定期完善)
##### 1. 获取url上的参数
```
  /**
   *获取url上的参数
   * @return {object}
   * @example
   * getRequest()  getRequest().paramA
   */
function getRequest() {
    var searchString = window.location.search.substring(1),
        params = searchString.split("&"),
        hash = {};
    if (searchString == "") return {};
    for (var i = 0; i < params.length; i++) {
        var pos = params[i].indexOf('=');
        if (pos == -1) { continue; }
        var paraName = params[i].substring(0, pos),
            paraValue = params[i].substring(pos + 1);
        hash[paraName] = paraValue;
    }
    return hash;
}
```
 返回一个对象，将url上的参数以键值对的形式存储在返回结果中，如果url上没参数，则返回空对象
##### 2. 计算两个日期的时间差
```
  /**
   * 计算两个日期时间的时间差
   * @param {Date, Date}  date1 date2
   * @return {object | null} 
   * @example
   * getDiff(new Date('2017-09-08'), new Date())
   */
function getDiff(date1, date2) {
     if (!date1.getTime || !date2.getTime) return null
     var ms = (date1.getTime() - date2.getTime());
     var day1 = Math.round(ms / 24 / 3600 / 1000),
         hh1 = Math.round((ms / 3600 / 1000) % 24),
         mm1 = Math.round((ms / 1000 / 60) % 60),
         ss1 = Math.round((ms / 1000) % 60);
     return {
         day: day1,
         hour: hh1,
         minute: mm1,
         second: ss1
      };
}
```
 传入两个Date日期对象，返回一个对象，其属性值day、hour、minute、second分别表示相差天数、小时、分钟、秒。结果以Math.round()取整，如果结果为负，则表示第一个日期在第二个日期前面
##### 3. 将canvas转化为image图片格式
```
  /**
   * 将canvas转化为image格式
   * @param {string}  cId
   * @return {object HTMLImageElement} 
   * @example
   * canvasToImg('canvas')  canvasToImg('#canvarsId')
   */
function canvasToImg(cId){
    let canvas = document.querySelector(cId)
    if (!canvas || !canvas.toDataURL) return new Image()
    let imgData = canvas.toDataURL('image/png'),
        imgs= new Image();
        imgs.src=imgData;
    return imgs
}
```
 传入一个css选择器，函数根据选择器查询canvas节点，然后返回该canvas的image格式节点，如果查找不到则返回一个空的image。原理是将canvas转化为base64编码，toDataURL方法貌似是canvas节点独有的，然后新建一个src是这个base64编码的图片。
 ps:什么情况下需要做这种转换呢？目前我知道的一个就是canvas在移动端无法长按保存到手机。
##### 4. 生成随机guid
```
  /**
   * 生成一个唯一的guid
   * @return {string}
   * @example
   * // 7f603b20-17ff-4f47-aeb9-e7996de04939
   * util.guid();
   * @see http://stackoverflow.com/questions/105034/create-guid-uuid-in-javascript
   */
  function guid () {
    return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
      var r = Math.random()*16|0, v = c == 'x' ? r : (r&0x3|0x8);
      return v.toString(16);
    });
  }
```
 这个方法用于生成一个随机[guid](https://baike.baidu.com/item/GUID/3352285?fr=aladdin)，可以将生成的guid视为全局唯一的(生成两个相同id的情况很少)。guid似乎在前端用的比较少，目前项目用到就是在每次请求后端接口时调用此方法，生成一个guid传过去。
##### 5. 获取一个月份的天数
```
  function isLeapYear (year) {
    if (year % 100 === 0) {
      if (year % 400 === 0) {
        return true;
      }
    } else if (year % 4 === 0) {
      return true;
    }
    return false;
  }
  /**
   * 获取某个月份有多少天
   * @return {number}
   * @param {string | number}  year month
   * @example
   * getDaysInMonth(2017, 9)
   */
  function getDaysInMonth (year, month) {
    return [31, isLeapYear(year) ? 29 : 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31][month];
  }
```
 传入一个年份和月份，返回该月有多少天，其中也包含了一个isLeapYear方法来判断是否是闰年，应该在实现日历或者日期选择组件时用的到
#### 结语
 暂时就写这么多了，后面不定期补充。