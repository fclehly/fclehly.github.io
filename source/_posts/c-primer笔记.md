---
title: c++primer笔记
date: 2018-06-30 15:02:00
tags:
- c++
categories:
- c++
- book reading
thumbnail: "http://p0.qhimg.com/t017ae756a191647d56.png"
---
# 阅读c++ primer
## Chapter 1 开始
执行完程序可以通过echo命令获得返回值：
Linux:  
> echo $?

Windows:  
> echo %ERRORLEVEL%

**cerr** 输出警告和错误信息，ostream对象，写到cerr的数据不缓冲。  
**clog** 输出一般性信息，ostream对象，写到clog的数据缓冲，存入日志文件。

## Chapter 2 变量和基本类型
#### 变量声明和定义
变量能且只能被定义一次，但是可以被多次声明。  
#### 引用
引用并非对象，它只是为一个已存在的对象所起的另外一个名字。
#### 指针
**nullptr** 是一种特殊类型的字面值，它可以被转换成任意其他的指针类型。
NULL的值就是0，在cstdlib中定义。  
> 建议： 初始化所有指针

**void***是一种特殊的指针类型，可用于存放任意对象的地址，我们对该地址中到底是个什么类型的对象并不了解。

#### const
顶层const(top-level const)表示指针本身是个常量；
底层const(low-level const)表示指针所指的对象是一个常量；

#### 常量表达式
如果认定变量是一个常量表达式，那就把它声明为constexpr类型；

一个constexpr指针的初始值必须是nullptr或0，或存储于摸个固定地址中的对象；
```cpp
constexpr int mf = 20;
const int limit = mf + 1;
constexpr int sz = size(); \\size是一个constexpr函数
```

#### 类型别名
1. 关键字 **typedef**：
> typedef double base, \*p;
2. 别名声明(c++11):
> using SI = Sales_item;
