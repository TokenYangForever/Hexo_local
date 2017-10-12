title: 'mysql-workbench中PK,NN,UQ,BIN,UN,ZF,AI标识简写说明'
tags:
  - mysql
categories:
  - 后端
author: Token
date: 2017-09-15 13:36:00
---

---
* 使用mysql workbench建表时，可以设置字段标识：PK,NN,UQ,BIN,UN,ZF,AI。他们的分别表示的意思是：
PK：primary key 主键
NN：not null 非空
UQ：unique 唯一索引
BIN：binary 二进制数据(比text更大)
UN：unsigned 无符号（非负数）
ZF：zero fill 填充0 例如字段内容是1 int(4), 则内容显示为0001 
AI：auto increment 自增
