title: JavaScript中的 抽象语法树 AST
author: TokenYangForever
tags:
  - JavaScript
categories:
  - JavaScript
date: 2017-11-17 17:40:00
---
### AST
* 抽象语法树（Abstract Syntax Tree）也称为AST语法树，指的是源代码语法所对应的树状结构。也就是说，一种编程语言的源代码，通过构建语法树的形式将源代码中的语句映射到树中的每一个节点上。 ![示意图.png](http://upload-images.jianshu.io/upload_images/6383319-b70c4cc018199852.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 程序代码本身可以被映射成为一棵语法树，而通过操纵语法树，我们能够精准的获得程序代码中的每一个精确的节点。例如声明语句，赋值语句，而这是用正则表达式所不能准确体现的地方。[esprima](http://esprima.org/demo/parse.html#)提供了一个在线解析JavaScript代码的地址，可以清楚地观察到js代码被转化为JSON格式，由一个个具体的符号组成

#### 作用
* 抽象语法树的作用非常的多，比如编译器、IDE、压缩优化代码等。在JavaScript中，虽然我们并不会常常与AST直接打交道，但却也会经常的涉及到它。例如使用UglifyJS来压缩代码，实际这背后就是在对JavaScript的抽象语法树进行操作。 
![示意图2.png](http://upload-images.jianshu.io/upload_images/6383319-09faa2604367b873.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
