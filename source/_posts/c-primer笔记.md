---
title: c++primer笔记
date: 2018-06-30 15:02:00
tags:
- reading
- c++
categories:
- Note
thumbnail: "http://p0.qhimg.com/t017ae756a191647d56.png"
---
# 阅读c++ primer
[C++手册](http://www.cplusplus.com/)
## Chapter 1 开始
执行完程序可以通过echo命令获得返回值：
Linux:  
`echo $?`

Windows:  
`echo %ERRORLEVEL%`

**cerr** 输出警告和错误信息，ostream对象，写到cerr的数据不缓冲。  
**clog** 输出一般性信息，ostream对象，写到clog的数据缓冲，存入日志文件。

## Chapter 2 变量和基本类型
### 变量声明和定义
变量能且只能被定义一次，但是可以被多次声明。  
### 引用
引用并非对象，它只是为一个已存在的对象所起的另外一个名字。
### 指针
**nullptr** 是一种特殊类型的字面值，它可以被转换成任意其他的指针类型。
**NULL**的值就是0，在**cstdlib__**中定义。  
> 建议： 初始化所有指针

**void***是一种特殊的指针类型，可用于存放任意对象的地址，我们对该地址中到底是个什么类型的对象并不了解。

### const
顶层const(top-level const)表示指针本身是个常量；
底层const(low-level const)表示指针所指的对象是一个常量；

### 常量表达式
如果认定变量是一个常量表达式，那就把它声明为constexpr类型；

一个**constexpr**指针的初始值必须是**nullptr**或0，或存储于摸个固定地址中的对象；
```cpp
constexpr int mf = 20;
const int limit = mf + 1;
constexpr int sz = size(); //size是一个constexpr函数
```

### 类型别名
1. 关键字 **typedef**：

```cpp
typedef double base, \*p;
```

2. 别名声明(c++11):

```cpp
using SI = Sales_item;
```

### auto类型说明符

**auto** 一般会忽略顶层const，同时底层const会保留下来：

```cpp
int i = 0;
const int ci = i, &cr = ci;
auto b = ci;  //b是int
auto c = cr;  //c是int
auto d = &i;  //d是int*
auto e = &ci; //e是const int*
```
如果希望推断出的auto类型是一个顶层const，需要明确指出：
```cpp
const auto f = ci;
```

### decltype类型指示符

```cpp
decltype (f()) sum = x; //sum的类型就是函数f的返回类型，编译器并不实际调用函数f，而是使用当调用发生时f的返回值作为sum的类型。
```

**decltype** 处理顶层const：如果decltype使用的表达式是一个变量，则decltype返回该变量的类型（包括顶层const和引用）：
```cpp
const int ci = 0, &cj = ci;
decltype(ci) x = 0; //x的类型是const int
decltype(cj) y = x; //y的类型是const int&，y绑定到变量x
decltype(cj) z; //错误：z是一个引用，必须初始化
```

> 切记：decltype((variable))的结果永远是引用，而decltype(variable)结果只用当variable本身是一个引用时才是引用；


## Chapter 3 字符串、向量和数组
### 命名空间的using声明
```cpp
//...
using std:cin;
//....
cin >> i;
```
> 头文件不应该包含using声明

### size_type类型
string:size()、vector:size()等函数返回size_type类型，无符号整数，与机器无关。

### size_t类型
使用数组下标时，通常定义为size_t类型，size_t类型时一种机器相关的无符号类型，它被设计得足够大以便能表示内存中任意对象的大小。

### 迭代器
**const_iterator** 只能读元素，不能写元素；
(c++11)cbegin()和cend()返回const_iterator;

## Chapter 4 表达式
### 命名的强制类型转换
1. **static_cast**：
任何具有明确定义的类型转换，只要不包含底层const，都可以使用static_cast。将较大的算术类型赋值给较小的类型。
2. **const_cast**:
const_cast智能改变运算对象的底层const；将常量对象转换为非常量对象或逆操作；常用于函数重载的上下文中。
3. **reinterpret_cast**:
reinterpret_cast通常为运算对象的位模式提供较低层次上的重新解释；

## Chapter 5 语句
### 异常
```cpp
throw runtime_error("....");
```
类型runtime_error定义在 **stdexcept** 头文件中；
stdexcept头文件中定义的异常类有：
- exception,
- runtime_error,
- range_error,
- underflow_error,
- overflow_error,
- logic_error,
- domain_error,
- invalid_argument,
- length_error,
- out_of_range;

## Chapter 6 函数
形参尽量使用常量引用；
### 可变形参
**initializer_list** 形参:initializer_list是一种模板类型；
```cpp
void error_msg(initializer_list<string> il) {}
```

### 引用返回左值
调用一个返回引用的函数得到左值，其他返回类型得到右值。

### 列表初始化返回值
C++11：函数可以返回花括号包围的值的列表。
```cpp
vector<string> f() {
  //...
  return {"1", "s2"};
}
```

### 主函数main的返回值
定义在 **cstdlib** 头文件中：
**EXIT_FAILURE**, **EXI_SUCCESS**;

### 声明一个返回数组指针的函数

`Type (*function(parameter_list))[dimension]`
例如：
`int (*func(int i))[10];`

### 使用尾置返回类型
C++11：tailing return type：  
`auto func(int i) -> int(*)[10];`

### 默认实参
`string f(string, string, char = '*')`

### constexpr函数
constexpr函数的返回类型和所有形参都得是字面值类型，函数体中有且只有一条return语句;
`constexpr int sz() {return 1;}`
内联函数和constexpr函数通常定义在头文件中。

###  调试帮助
如果定义了NDEBUG，那么assert什么也不做；  
`CC -D NDEBUG main.cpp`
- `__func__`：当前函数的名字
- `__FILE__`：存放文件名的字符串字面值。
- `__LINE__`：存放当前行号的整形字面值。
- `__TIME__`：存放文件编译时间的字符串字面值。
- `__DATE__`：存放文件便宜日期的字符串字面值。

### 函数指针
`bool (*pf)(int, int)`

## Chapter 7 类
### =default
C++11：如果我们需要默认的行为，那么可以通过在参数列表后写 **= default** 来要求编译器生成构造函数。

### 可变数据成员
```cpp
class Screen {
public:
  void some_member() const;
  //...
private:
  mutable size_t access_ctr;  //即使在一个const对象中也能被修改
};
void Screen::some_member() const {
  ++access_ctr;
  //...
}
```

### 返回*this

```cpp
class Screen {
public:
  Screen &set(char);
  Screen &set(pos, pos, char);
  //...
};

inline Screen &Screen::set(char c) {
  //...
  return *this; //将this对象作为左值返回
}

inline Screen &Screen::set(pos r, pos l, char c) {
  //...
  return *this;
}

myScreen.set('#').set(1, 2, '*');
```

一个const成员函数如果以引用的形式返回*this，那么它的返回类型将是常量引用；

###
