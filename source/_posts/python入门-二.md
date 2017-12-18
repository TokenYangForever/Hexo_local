title: python入门(二)
author: TokenYangForever
tags:
  - python
categories:
  - 后端
  - python
date: 2017-12-18 17:08:00
---
### 函数
* python中自带一些全局函数，比如在js中封装在math对象的数学方法：max、min、abs，也有可以用于数据转换的函数：int、str；用于遍历的方法：all、any。官网上可以看到[https://docs.python.org/3/library/functions.html](https://docs.python.org/3/library/functions.html)
#### 定义函数
* def 用来定义函数。
* isinstance()方法可以用来判断参数类型，第一个参数是需要判断的对象，第二参数是候选的变量类型，多个类型之一的话要用tuple类型表示
```
isinstance(x, (int, float))
```
#### 定义空函数
* pass相当于一个占位符，表示什么也不做
```
def nop():
    pass
```
#### 设置函数的默认参数
* 使用等号设置参数的默认值，和es6语法一致。需要注意的是：必选参数在前，默认参数在后
* 定义默认参数要牢记一点：默认参数必须指向不变对象！
```
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```
#### 定义可变参数
* 类似于es6中的...(function (...args) {})，这里的numbers是一个tuple类型的数据
```
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```
* 传入参数时也可以使用*把数组[]和元组()类型的参数 => 变成可变参数
```
calc(*[1,2,3], *(4,5,6))
```
#### 关键字参数
* python中传入参数时，可以用关键字来修饰参数，这样甚至就可以不用考虑传参的顺序。
* 未出现在参数中的关键字参数会进入other(一个dict类型的数据)里面，同样的也可以使用**来讲dict类型的数据转换成可变参数传入。
* 注意：多余的未知参数会进入到args中，多余的关键字参数则会进入到other中
```
def person(name, age, *args, **other):
    print('name:', name, 'age:', age, 'other:', other, 'args:', args)

# person(age=45, name='Adam')
person('Adam', 45, 123, 456, yaho='lalala', **{'city':'Beijing', 'job':'Engineer'})
```
* 但是使用关键字参数时有很多限定条件：关键字参数必须放在一般参数的后面
#### 命名关键字参数
* 定义参数时，跟在*或者可变参数(*args)后面的参数，被视为命名关键字参数，这些参数在调用函数传参时，**必须传入参数名。**
```
def person(name, age, *或者*args, city, job):
    print(name, age, city, job)
person('Adam', 45, city='Beijing', job='Engineer')
```
#### 参数组合
* 在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。
#### 说一句递归
* python并不能实现尾调用优化，递归调用函数时，每一次调用都会增加一次栈帧，超过上线后就会报错。