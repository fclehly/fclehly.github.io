---
title: Thinking in Function Programming
date: 2018-12-16 15:23:36
tags:
- Thinking
- Programming
---

# Thinking in Function Programming


## Functor
> TODO: explain it

### No Side Effect
> TODO: explain it
### Currying
> TODO: explain it
### Lazy Evaluation
> TODO: explain it
### Closure
> TODO: explain it

## Implements

### In Python
> Python并不是函数式编程语言, 只是支持一些函数式编程的特性


### In Java
Java在8之前不支持函数式编程。Java8新增Stream API支持以一种声明的方式处理数据；
例如：
```java
// java11
var list = List.of("a", "b", "c", "1");
var listUpper = list.parallelStream()
                    .filter(s -> !s.isEmpty())
                    .map(s -> s.toUpperCase())
                    .collect(Collectors.toList());
var sortedList = list.stream()
                    .sorted()
                    .collect(Collectors.joining(","));
```

### In JS

## Reference
1. [Why Functional Programming Matters](http://www.cse.chalmers.se/~rjmh/Papers/whyfp.html); 