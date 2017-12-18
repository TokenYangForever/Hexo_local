title: python入门(一)
author: TokenYangForever
tags:
  - python
categories:
  - 后端
  - python
date: 2017-12-14 20:53:00
---
### Python简介
* Python是用来编写应用程序的**高级**编程语言
* Python为我们提供了非常完善的基础代码库，覆盖了网络、文件、GUI、数据库、文本等大量内容，被形象地称作“内置电池（batteries included）”
### Python特(缺)点
* 第一个缺点就是运行速度慢，和C程序相比非常慢，因为Python是**解释型语言**，你的代码在执行时会一行一行地翻译成CPU能理解的机器码，这个翻译过程非常耗时，所以很慢。而C程序是运行前直接编译成CPU能执行的机器码，所以非常快。
* 第二个缺点就是代码不能加密。如果要发布你的Python程序，实际上就是发布源代码，这一点跟C语言不同，C语言不用发布源代码，只需要把编译后的机器码（也就是你在Windows上常见的xxx.exe文件）发布出去。要从机器码反推出C代码是不可能的，所以，**凡是编译型的语言，都没有这个问题，而解释型的语言，则必须把源码发布出去。**
### Python 安装
* mac上面:安装Homebrew后，直接通过命令brew install python3安装即可。
### 编辑器和解释器
* python是解释型语言，使用上面的命令安装后就有自带的解释器。在命令行输入python可以进入python交互模式，类似于node。输入python3 文件名.py即可执行python文件
* 文本编辑器使用sublime Text即可
### python基础语法
* Python的语法比较简单，采用缩进方式，循环条件语句都不需要括号
* Python区分大小写
* python中能直接处理的数据类型：整数、浮点数、字符串、布尔值、空值(None)。也有变量，变量不需要声明类型。
* 这种变量本身类型不固定的语言称之为动态语言，与之对应的是静态语言。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错(比如java)。
* 一些操作符：and == && . . . or == || . . . 10 // 3 = 1 (Math.floor(10/3))
* str通过encode()方法可以编码为指定的bytes，相反的有decode方法
* 使用 % 来格式化字符串
```
'Hi, %s, you have $%d.' % ('Michael', 1000000) 
```
#### list和tuple
python内置list类型(相当于数组)；有序列表：tuple(元组)，tuple一旦初始化就不能修改；只有1个元素的tuple定义时必须加一个逗号，来消除歧义
#### dict和set
* Python内置了字典：dict的支持，dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。
```
d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}
d['Michael']
```
* dict获取值的方法：get()获取，第二个参数是取不到的时候返回值避免报错，key in d也可以判断dict中是否有对应的键值。不能用点.操作符！
```
d.get('Thomas', -1) 
```
* dict与list区别：dict查找和插入速度快，但是占用空间和资源更多。
* dict可以用在需要高速查找的很多地方，在Python代码中几乎无处不在，正确使用dict非常重要，需要牢记的第一条就是dict的key必须是不可变对象。
* set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。(只是一组无重复的不可变对象的key？)
### 小结
* 这里我就对python和JavaScript在语法上进行一个初步的比较：
1. 两者都是解释形的弱类型语言，变量的类型可随时改变。
2. 在python中的数据类型更多：比如类似于js数组的就有list/tuple。他们之间的比较后面有时间我再深入了解下。
3. 条件语句以及循环语句都是类似的，但是在python中没有用大括号{}来表示层级结构，而是通过冒号:以及缩进来控制的。