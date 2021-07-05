---
title: Unittest
date: 2021-06-27 21:28:59
tags:
---


## 单元测试基本原则

宏观上，单元测试要符合AIR原则；微观上，单元测试的代码层面要符合BCDE原则

### AIR 原则

单元测试的价值在业务代码线上运行时，难以感觉到器测试用例的存在和价值，但在代码质量的保障上，却是非常关键的。新增代码应该同步增加测试用例，修改代码逻辑是也应该同步保证测试用例成功执行。 AIR原则具体包括：

- A: 自动化 Automatic
- I: 独立性 Independent
- R: 可重复 Repeatable

单元测试应该是全自动执行的。测试用例通常会被频繁的触发执行，执行过程必须完全自动化才有意义。如果单元测试的输出结果需要人工介入检查，那么它一定是不合格的。单元测试中不允许使用控制台输出来进行人工验证，而必须使用断言来验证。

为了保证单元测试稳定可靠且便于维护，需要保证其独立性。用例之间不允许相互调用，也不允许出现执行次序的先后依赖。 如: testFunc2()需要调用testFunc1()，在执行testFunc2时会重复执行验证testFunc1，导致运行效率降低，且testFun1的验证失败会影响testFun2的执行。通常主流的测试框架中，测试用例的执行顺序都是无序的。

单元测试时可以重复执行的，不能收到外界环境的影响。比如单元测试通常会被放到持续集成中，每次有代码提交时单元测试都会被触发执行。如果单测对外部环境（网络、服务、中间件等）有依赖，则容易导致持续集成机制的不可用。




### BCDE 原则

编写单元测试时要保证测试粒度足够小，这样有助于精确定位问题，单元测试用例默认是方法级别的。单测不负责检查跨类或者跨系统的交互逻辑，那是集成测试需要覆盖的范围。编写单元测试用例时，为了保证北侧模块的交付质量，需要符合BCDE原则。

- B: Border，边界值测试，包括循环便捷、特殊取值、特殊时间点、数据顺序等。
- C: Correct，正确的输入，并得到预期的结果。
- D: Design，与设计文档相结合，来编写单元测试。
- E: Error，单元测试的目的是证明程序有错，而不是程无错。为了发现代码中潜在的错误，我们需要在编写测试用例时有一些强制的错误输入（如非法数据、异常流程、非业务允许输入等）来得到预期的错误结果。

由于单元测试只是系统集成测试前的小模块测试，有些因素往往是不具备的，因此需要进行Mock，例如：
1. 功能因素。如：被测试方法内部调用的功能不可用。
2. 时间因素。如：关键日企还没到来，与此时间相关的功能点。
3. 环境因素。如：政策环境，多端环境；
4. 数据因素。如：线下数据样本过小，难以覆盖各种线上真是场景。
5. 其他因素。为了简化测试编写，开发者也可以将一些负责的依赖采用Mock方式实现。

最简单的Mock方式是硬编码，更为优雅的方式是使用配置文件，最佳的方式是使用相应的Mock框架。Mock的本质是让我们写出更稳定的单元测试，隔离上述因素对单元测试的影响，使结果变得可预测，做到真正的单元测试。

## 单元测试覆盖率

单元测试是一种白盒测试，测试者依据程序的内部结果来实现测试代码。单测的覆盖率是指业务代码被单测测试的比例和程度，它是衡量单元测试好坏的一个很重要的指标，各种覆盖率从粗到细、从弱到强排列如下：


粗粒度的覆盖
- 类覆盖
- 方法覆盖

细粒度的覆盖
- 行覆盖
- 分支覆盖
- 条件判定覆盖
- 条件组合覆盖
- 路径覆盖


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