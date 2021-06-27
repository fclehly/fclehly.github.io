---
title: Unittest
date: 2021-06-27 21:28:59
tags:
---


##  golang

### testing

```go
func TestAdd(t *testing.T) {
    got := Add(10)
    if got == 11 {
        t.Errorf("wrong result of Add(10)")
    }
}
```

- 每个test文件必须import一个testing
- test文件文件下的每一个testcase 均必须以Test开头并且符合TestXxxx形式，否则go test会直接跳过测试不执行；
- testcase 的入参为`t *testing.T`或`b *testing.B`
- `t.SkipNow()`为跳过当前test，并且直接按pass处理继续下一个test
  
  如果遇到testcase不想跑，或者testcase暂时有问题但是短时间内fix不了而以后肯定是会用的，此时会用`t.SkipNow()`跳过当前testcase并继续下一个testcase。

  `t.skipNow()`一定要写在testcase的第一行。写在中间或者末尾则不起作用
- go的test不会保证多个testcase是顺序执行的，但通常会按顺序执行。
  
  当遇到多个testcase可能会互相依赖，比如testcase1的结果作为testcase2的输入，或者多个testcase必须得按顺序执行同时结果也得按顺序输出，此时testcase是有顺序的，可以使用subtests的方式解决。

  使用`t.Run`来执行subtests可以控制testcases的输出以及执行顺序；

  ```go
  func TestPrint(t *testing.T) {
      t.Run("a1", func(t *testing.T) { fmt.Println("a1"))t.Run("a2", func(t *testing.T) { fmt.Println("a2"))
      t.Run("a3", func(t *testing.T) { fmt.Println("a3"))
  }
  ```

- 使用TestMain作为初始化test，并且使用`m.Run()`来调用其他tests可以完成一些需要初始化操作的testing，比如数据库连接、文件打开、服务登录等。
  
  ```go
  func TestMain(m *testing.M) {
      fmt.Println("run test main first")
      m.Run()
  }
  ```

  如果没有在TestMain中调用`m.Run()`，则除了TestMain以外的其他tests都不会执行



### BenchMark

- benchmark函数一般以`Benchmark`为前缀
- benchmark函数一般会会跑b.N次，而且每次执行都会如此
- 在执行过程中会根据实际case的执行时间是否稳定来增加b.N的次数已达到稳态


```
$ go test -bench=.
```

```go
func BenchmarkAll(b *testing.B) {
    for n := 0; n < b.N; n++ {
        Add(n)
    }
}
```

```go
func unstable(n int) int {
    if n > 0 {
        n--
    }
    return n
}
func BenchMarUnstable(b *testing.B) {
    for n := 0; n < b.N; n++ {
        unstable(n)
    }
}
```