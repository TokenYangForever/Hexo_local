title: javascript常用工具函数总结
tags:
  - JavaScript
  - 总结
categories:
  - JavaScript
  - 总结
author: Token
date: 2017-09-12 16:18:00
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
* 返回一个对象，将url上的参数以键值对的形式存储在返回结果中，如果url上没参数，则返回空对象
##### 2. 追加url参数
```
  /**
   * 追加url参数
   * @param {string} url url参数
   * @param {string|object} key 名字或者对象
   * @param {string} value 值
   * @return {string} 返回新的url
   * @example
   * appendQuery('lechebang.com', 'id', 3);
   * appendQuery('lechebang.com?key=value', { cityId: 2, cityName: '北京'});
   */
  function appendQuery (url, key, value) {
    var options = key;
    if (typeof options == 'string') {
      options = {};
      options[key] = value;
    }
    options = $.param(options);
    if (url.includes('?')) {
      url += '&' + options
    } else {
      url += '?' + options
    }
    return url;
  }
```
* 传入一个url和需要添加的参数键值对，需添加的参数可以直接传对象格式。会判断原url上是否有参数，没有的话就加'?'，返回添加参数后的url。
##### 3. 计算两个日期的时间差
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
* 传入两个Date日期对象，返回一个对象，其属性值day、hour、minute、second分别表示相差天数、小时、分钟、秒。结果以Math.round()取整，如果结果为负，则表示第一个日期在第二个日期前面
##### 4. 将canvas转化为image图片格式
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
* 传入一个css选择器，函数根据选择器查询canvas节点，然后返回该canvas的image格式节点，如果查找不到则返回一个空的image。原理是将canvas转化为base64编码，toDataURL方法貌似是canvas节点独有的，然后新建一个src是这个base64编码的图片。
* ps:什么情况下需要做这种转换呢？目前我知道的一个就是canvas在移动端无法长按保存到手机。
##### 5. 生成随机guid
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
* 这个方法用于生成一个随机[guid](https://baike.baidu.com/item/GUID/3352285?fr=aladdin)，可以将生成的guid视为全局唯一的(生成两个相同id的情况很少)。guid似乎在前端用的比较少，目前项目用到就是在每次请求后端接口时调用此方法，生成一个guid传过去。
##### 6. 获取一个月份的天数
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
* 传入一个年份和月份，返回该月有多少天，其中也包含了一个isLeapYear方法来判断是否是闰年，应该在实现日历或者日期选择组件时用的到
##### 7. 过滤对象属性
```
  /**
   * 过滤选出一个对象的某些属性
   * @param{object, array} obj key
   * @return{object}
   * @example
   * pick(obj, [key1, key2])
   */
  function pick (obj, keys) {
    let result = {}
    if (!obj || !keys.forEach) {
      return result
    }
    keys.forEach((item) => {
      if (obj.hasOwnProperty(item)) {
        result[item] = obj[item]
      }
    })
    return result
  }
```
* 传入一个对象和一个数组，遍历数组，如果传入对象有数组中包含的属性，则提取出来，返回一个过滤提取出来的属性组成的对象。
* 这个方法用处挺多的，比如从一个接口拿到了结果对象，可能上面很多属性你暂时用不到的，就可以用这个方法进行过滤。比如以前我用vue写项目的时候，就经常把接口返回的一个对象作为vue的data属性去渲染页面，这个时候这个对象是动态绑定在vue实例上面的，但是其中有些属性页面渲染根本用不到，还得去监听这些属性，白白浪费性能，这种时候将接口返回的对象筛选一下再赋给vue的data就显得合理多了。
##### 8. 判断是否是一个对象
```
  /**
   * 判断传入参数是否是一个合法对象
   * @param{object} obj 
   * @return{object}
   * @example
   * isObject (null) isObject (() => {} )
   */
function isObject (obj) {
    var type = typeof obj;
    return type === 'function' || type === 'object' && !!obj;
  }
```
* 判断传入参数是否是一个合法对象，function类型均返回true，null返回false(typeof null 返回 object)
##### 9. 判断一个函数是否是native code
```
  /**
   * 判断传入函数是否在当前环境下得到支持
   * @param{function} Ctor 
   * @return{boolean}
   * @example
   * isNative (window.Symbol)  isNative (window.Promise)
   */
function isNative (Ctor) {
  return typeof Ctor === 'function' && /native code/.test(Ctor.toString())
}
```
* javascript原生自带的方法使用toString转成字符串时会包含‘native code’字段，比如Math.max.toString()就返回"function max() { [native code] }"。
* 这个方法可以判断某些原生特性是否被当前浏览器支持。比如isNative (window.Promise)在chrome中返回true，因为chrome支持原生es6语法，放到ie里面就只能返回false了~
##### 10. 深度克隆对象
```
  /**
   * 返回传入对象的一个深度克隆的副本对象
   * @param{object} obj 
   * @return{object}
   * @example
   * cloneDeep(obj)
   */
  function cloneDeep (obj) {
    if (!isObject(obj)) return obj;
    let result
    if (Array.isArray(obj)) {
      result = []
      obj.forEach((item) => {
        result.push(cloneDeep(item))
      })
      return result
    }
    result = {}
    for (let key in obj) {
      let item = obj[key]
      if (_.isObject(item)) {
        result[key] = cloneDeep(item)
      } else {
        result[key] = item
      }
    }

    return result
  }
```
* 首先对传入参数进行判断，不是合法对象则直接返回其本身。然后再遍历其属性，属性中包含对象的会再次递归调用cloneDeep方法，浅克隆的话可以直接用Object.assign()方法。
##### 11. 获取两个地点的实际距离
```
/**
   * 获取两个高德坐标的距离, 后一个点，不传，默认为用户坐标
   * @return {null|number} 距离多少米，没有定位信息，返回null
   * @example
   * getDistance(31.282055633974, 121.379623888259)
   */
  function getDistance (endLat, endLon, startLat, startLon) {
    if (!startLat) {
      let address = Lizard.state.address

      if (address && address.lat) {
        startLat = address.lat
        startLon = address.lon
      }
    }

    // 没有定位
    if (!startLat) {
      return null
    }

    const PI = Math.PI
    let lon1 = (PI / 180) * startLon
    let lon2 = (PI / 180) * endLon 
    let lat1 = (PI / 180) * startLat  
    let lat2 = (PI / 180) * endLat 
    // 地球半径  
    let R = 6378.137;  

    // 两点间距离 km，如果想要米的话，结果*1000就可以了  
    let d = Math.acos(Math.sin(lat1) * Math.sin(lat2) + Math.cos(lat1) * Math.cos(lat2) * Math.cos(lon2 - lon1)) * R;  

    return parseInt(d * 1000, 10)
  }
```
* 依次传入两个点的经纬度，然后计算得出两个点的距离，单位为km。这个方法可能实际应用的比较少，我这里还是写出来，作为一种前端计算距离的方法(在能拿得到定位的情况下)。
##### 12. 加载图片(promise封装)
```
/**
   * 传入图片url，返回一个promise对象，加载成功时resolve
   * @return {Promise} 
   * @example
   * loadImg(url).then(console.log('加载完成')).catch(err => {console.log(err)})
   */
  function loadImg (url) {
    return new Promise((resolve, reject) => {
      let img = new Image()

      img.addEventListener('load', function() {
        resolve([img.width, img.height])
      }, false)

      img.addEventListener('error', reject, false)

      img.src = url
    })
  }
 ```
* 这个函数参考自ECMAScript 6 入门，算是promise的一个很经典的应用实例，加载完成后promise对象resolve，同时返回图片的宽高，以便后面调整样式等等。加载失败则返回reject，所以再调用这个方法后需要用catch方法来捕捉异常。
##### 13. 重复字符串n次
```
/**
   * 传入字符串，和重复次数，返回结果字符串
   * @return {string} 
   * @param{string, number} str n 
   * @example
   * loadImg(url).then(console.log('加载完成')).catch(err => {console.log(err)})
   */
  const repeat = (str, n) => {
    let res = ''
    while (n) {
      if (n % 2 === 1) res += str
      if (n > 1) str += str
      n >>= 1
    }
    return res
  }
```
* 这个函数在vue源码里看到的，用到了位运算符，如果让我来实现的话可能就马上想到重复n次，而上面这个函数执行时的时间复杂度差不多只是n/2。
##### 14. 变量是否以'$'或者'_'开头
```
/**
   * 传入字符串，判断是否以'$'或者'_'开头
   * @return {Boolean} 
   * @param{string} str
   * @example
   * isReserved (‘$’)  isReserved (‘param’)
   */
function isReserved (str) {
  var c = (str + '').charCodeAt(0);
  return c === 0x24 || c === 0x5F
}
```
* 同样是vue源码里的方法，判断变量是否以'$'或者'_'开头，charCodeAt(index)方法会返回字符串对应index位置的字符的Unicode编码，'$'对应的Unicode编码就是36，这里用的是十六进制表示方法就是0x24。要判断变量名以什么字符开头或结尾，将上面方法略微修改即可实现。
* ps：vue中哪里用到这个方法了呢，我去看了下就是在初始化vue实例的data对象时会判断，如果你的vue实例data里面命名了以'$'或者'_'的对象，是不会把这个属性重新定义其get/set的。简单点说，如果你在vue的data里定义一个命名为$data的属性，再在模板里渲染{{$data}}，就会报错的。
##### 15. promise扩展-finally
```
/**
   * 在一个promise链调用结尾调用finally，传入一个函数，无论最后promise的状态是什么总会执行该函数
   * @param{function} callback
   * @example
   *  server.listen(0).then(function () { // run test }).finally(server.stop);
   */
Promise.prototype.finally = function (callback) {
  let P = this.constructor;
  return this.then(
    value  => P.resolve(callback()).then(() => value).catch(e => {console.error(e)}),
    reason => P.resolve(callback()).then(() => { throw reason }).catch(e => {console.error(e)})
  );
};
```
* 参考自[ECMAScript 6 入门](http://es6.ruanyifeng.com/?search=find&x=0&y=0)，对于promise对象的一个扩展方法，不论前面的promise状态是什么总会调用callback函数。
* 我在原代码的基础上添加了新的catch，用于兼容callback执行时返回了一个状态为reject的promise的情况。
#### 结语
* 再次补充，已搬运到[github博客](https://tokenyangforever.github.io/2017/09/12/JavaScript%E4%B8%AD%E5%B8%B8%E7%94%A8%E5%B7%A5%E5%85%B7%E5%87%BD%E6%95%B0/)，上面自带导航~~~