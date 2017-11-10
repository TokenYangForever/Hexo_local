title: vue源码解析之--工具函数(一)
tags:
  - JavaScript
  - 框架源码
categories:
  - JavaScript
  - vue
author: Token
date: 2017-09-15 13:36:00
---

---
　工具函数是每个框架类库的基本的组成部分，本篇分析的是/shared/util.js文件，从中挑选的部分方法函数，比较常见基础的方法我就跳过了。值得一说的是，编译合成前的源码都用到了**flow.js**，可以理解为一个javascript的静态类型检查器，有点像typescript，可以对js的变量进行类型定义、检查错误等，然后再通过编译生成正常的js代码，文件开头有/* @flow */注释的都是用到了flow的。

#### 1.makeMap()
　先来看这个makemap，传入一个字符串和是否区分大小写的标示符，然后在函数内部生成一个map对象，传入字符串分割 ',' 生成的数组的值作为map的属性名，值为true，然后**返回一个函数用来判断一个变量是否包含在传入字符串里**。
<code>
function makeMap (
  str,
  expectsLowerCase
) {
  var map = Object.create(null);
  var list = str.split(',');
  for (var i = 0; i < list.length; i++) {
    map[list[i]] = true;
  }
  return expectsLowerCase
    ? function (val) { return map[val.toLowerCase()]; }
    : function (val) { return map[val]; }
}</code>
 　这里也是个典型的闭包的应用，返回的函数调用了内部的map数组，所以map的作用域会一直存在不会被回收掉。这个方法主要用在检查变量命名是否合法之类的。

#### 2.cached()
<code>function cached (fn) {
  var cache = Object.create(null);
  return (function cachedFn (str) {
    var hit = cache[str];
    return hit || (cache[str] = fn(str))
  })
}
</code>
　新建一个cache对象，这个方法传入参数是一个**处理字符串的函数**，返回这个**传入函数的缓存版本**。就是每次调用这个处理字符串函数时，会把被传入的字符串存到cache中，下次再传入相同字符串时就直接用cache缓存了(又是巧用闭包...)。
#### 3.camelize、capitalize、hyphenateRE
<code>var camelizeRE = /-(\w)/g;
var camelize = cached(function (str) {
  return str.replace(camelizeRE, function (_, c) { return c ? c.toUpperCase() : ''; })
});
var capitalize = cached(function (str) {
  return str.charAt(0).toUpperCase() + str.slice(1)
});
var hyphenateRE = /([^-])([A-Z])/g;
var hyphenate = cached(function (str) {
  return str
    .replace(hyphenateRE, '$1-$2')
    .replace(hyphenateRE, '$1-$2')
    .toLowerCase()
});</code>
　这三个函数都是**处理字符串**的，主要是对**正则表达式的应用**，结合上面提到的cache生成的缓存版函数camelize()用于将中划线命名的变成驼峰法命名，camelize('abc-def') = abcDef；capitalize()用于将首字母大写，hyphenateRE()作用与camelize()相反，用于将驼峰法命名转换成中划线。(这里没看懂为什么要用两次replace方法？)
### 4.bind()
<code>
function bind (fn, ctx) {
  function boundFn (a) {
    var l = arguments.length;
    return l
      ? l > 1
        ? fn.apply(ctx, arguments)
        : fn.call(ctx, a)
      : fn.call(ctx)
  }
  boundFn._length = fn.length;
  return boundFn
}</code>
　封装了一个简单的bind方法，根据传入的对象(作用域)和函数，返回一个绑定函数，即在传入的对象调用传入的函数。源码解释说比原生绑定更快，这里我也不太明白为什么。。。
### 5.genStaticKeys()
<code>
function genStaticKeys (modules) {
  return modules.reduce(function (keys, m) {
    return keys.concat(m.staticKeys || [])
  }, []).join(',')
}
</code>
　这个函数主要是把传入的**编译模块，生成为静态键值组成的字符串**。主要用到了**reduce**这个方法，即把数组每一项重叠生成一个总和，第二个参数[]表示初始值，生成的总数组用逗号分隔转换为字符串。我看了下似乎这个方法只是在定义baseOptions的staticKeys属性时用到了。
### 6.looseEqual() looseIndexOf()
<code>
function looseEqual (a, b) {
  var isObjectA = isObject(a);
  var isObjectB = isObject(b);
  if (isObjectA && isObjectB) {
    try {
      return JSON.stringify(a) === JSON.stringify(b)
    } catch (e) {
      // possible circular reference
      return a === b
    }
  } else if (!isObjectA && !isObjectB) {
    return String(a) === String(b)
  } else {
    return false
  }
}
function looseIndexOf (arr, val) {
  for (var i = 0; i < arr.length; i++) {
    if (looseEqual(arr[i], val)) { return i }
  }
  return -1
}</code>
　looseEqual()用于判断两个传入参数是否相等，同是对象的情况下先用**JSON.stringfiy()**转换，报错的时候直接用全等判断，这里顺便提一下什么时候JSON.stringfiy()会报错呢？**circular structure**的时候，举一个例子<code>var obj = {a:1}; obj.b=obj;</code> 这个时候JSON.stringfiy(obj)就会报错啦~都不是对象的时候转化为string比较，其他情况都是false。
　looseIndexOf()传入一个数组和一个值，如果传入值在数组里则返回对应的index，不在返回-1，这里也是依次用looseEqual来进行比较.ES6里面数组的扩展方法有一个indexOf也是类似的方法，判断数组是否包含某个值。
### 7.once()
<code>function once (fn) {
  var called = false;
  return function () {
    if (!called) {
      called = true;
      fn.apply(this, arguments);
    }
  }
}</code>
　让一个函数只调用一次，也是典型的闭包调用，返回一个由called标示控制的函数，调用此函数时，called标示符改变，下次调用就无效了。
