
---
title: 算法学习：Dynamic Programming
date: 2019-04-09 14:35:55
tags:
---

## 背景

> 动态规划（Dynamic Programming）是一种在数学、管理科学、计算机科学、经济学和生物信息学中使用的，通过把原问题分解为相对简单的子问题的方式求解复杂问题的方法。 动态规划常常适用于有重叠子问题和最优子结构性质的问题，动态规划方法所耗时间往往远少于朴素解法。 动态规划背后的基本思想非常简单。——wikipedia

最近陆续参加大厂的春招，发现许多大厂的笔试都会放出至少一道dp算法，可见dp算法在实际业务中的应用十分广泛。然后，我又不会做，所以还是写篇blog来记录一下学习过程。

## 例题
以下是非常经典的dp题目：

> 小明有1元，3元，8元，18元，67元的某种货币硬币各若干枚，现在他需要出门购买一份价值277元的礼物，请问他最少需要带多少枚硬币？

**约定：为方便，设f(x)=n；其中n为最后数量，x为价值。**

显然，我们的第一反应是从面值大的开始拿，先拿67的，拿不下了再换18的……以此类推，最后我们会拿 `67*4 + 8*1 + 1*1` 刚好拿完我们的277元，`f(277)=6`。显然这个结果来的太顺利了，如果我们现在的情况如下：

> 小明有1元，18元，67元的某种货币硬币各若干枚，现在他需要出门购买一份价值80元的礼物，请问他最少需要带多少枚硬币？

那完了，我们从大的开始拿需要 `67*1 + 1*13` ，此时`f(80)=14`，但是我们把各种情况代进去就会发现其实 `18*4 + 1*8` 这种情况下 `f(80)=12` 才是最优情况。事实上，在运用贪心算法解决问题的时候，我们必须首先证明该算法是最优算法（最安全）。 

## 解决思路
> dynamic programming is a method for solving a complex problem by breaking it down into a collection of simpler subproblems. ——Wikipedia

dp问题的本质在于去寻找各个子问题状态中的最优解，其中在大部分情况下，子问题可以理解为下一步（next step）。for一个sample：

我们面对这个80块，我们可以在下一步产生三种状态： `67*1 + 13` `18*1 + 62` 和 `1*1 + 79` ，这就是上一个问题的三个子问题。这个时候我们发现，我们不再需要关心上一个问题（如何凑80元）是怎么解决的了，现在我们知道，`f(80)` `f(13)+1` `f(62)+1` `f(79)+1`四个问题一定在最优解情况下相等，这就是dp问题的无后效性。

最后，在我们不断地“查询”子问题状态的时候，整个问题的选择将会变成一棵树，我们只需要查询树中从任一叶到根的最短路径的长度即可得出问题的解。

## 状态储存
给出一个再熟悉不过的计算n=5的斐波那契的代码(in Golang)

```Golang
func Fib(n int) int {
  if (n == 0){
    return 0
  }
  if (n == 1){
    return 1
  }
  return Fib(n-1) + Fib(n-2)
}

func main(){
  Fib(5)
}
```

这也是一个很明显的子问题可以画成一棵树的问题，但实际问题在于： `Fib(5)` 花费掉我608ns， `Fib(40)`花费掉我1.063243715s，`Fib(45)`花费掉我11.183974111s。显然，根据树的数据结构，我们计算递归子问题的时间随着子问题深度的变大而变得十分庞大，当算法要求给出的总数达到一定数量级之后，我们便永远不可能通过不断地递归调用而寻找子问题中的最优解。

**我们的时间花费在了哪里呢？**

很显然，我们的时间花费在了大量重复的计算上，很容易就能想到，这棵树有多少叶子节点，我们便计算了多少次 `Fib(0)` 的结果，往上推，这一系列重复的动作是从某几根重复的树枝延伸出来的，这个时候，我们需要将已经计算过的结果储存起来。

在进行运算的过程中，我们注意到，整个斐波那契函数的形参是由大变小的。所以，如果我们想要在计算中使用已经储存的结果，那么我们需要有小到大计算斐波那契函数的结果。

```Golang
var tmp []int

func Fib(n int) int {
  //有结果优先返回
  if (len(tmp) >= n){
    return tmp[n]
  }
  if (n == 0){
    tmp = append(tmp, 0)
    return 0
  }
  if (n == 1){
    tmp = append(tmp, 1)
    return 1
  }
  rs := Fib(n-1) + Fib(n-2)
  tmp = append(tmp, rs)
  return rs
}

func main(){
  Fib(45)
}
```

打扰了！上面代码的运行时间27.549µs，多次测试也稳定在30µs以内。差别巨大，至此为止，这个斐波那契函数我们基本可以认为是时间复杂度O(n)的算法。

## 解决问题
回来帮小明解决买东西的问题，我们仅仅需要将80元以下所有的情况都计算保存，并在计算下一个状态的时候考虑能否用大额硬币替换小额硬币，就能够把该问题通过一个时间复杂度O(n)的算法解决。

```Golang
var tmp []int

func GetNum(n int){
  //给一个足够大的初始值，可以是最大值
  num := 80
  if (n-1 >= 0){
    if (tmp[n-1]+1 < num){
      num = tmp[n-1] + 1
    }
  }
  if (n-18 >= 0){
    if (tmp[n-18]+1 < num){
      num = tmp[n-18] + 1
    }
  }
  if (n-67 >= 0){
    if (tmp[n-67]+1 < num){
      num = tmp[n-67] + 1
    }
  }
  //写入tmp
  if (len(tmp) <= n){
    tmp = append(tmp, num)
  }
}

func main(){
  //压入初始值tmp[0]
  tmp = append(tmp, 0)
  var i int
  //生成整个tmp
  for i=1; i<=80; i++ {
    GetNum(i)
  }
  fmt.Println(tmp[80])
}
```

## 一般化
实际题目中，会将总价x、货币数量以及面值，通过STDIN传入，并通过TestCase处理。下面是一般化题目：

```
小明有N种不同面值的某种货币硬币各若干枚，
现在他需要出门购买一份价值M元的礼物，
请问他最少需要带多少枚硬币？
题目输入的第一行给出两个数字分别为总价和货币面值的数量，
从后一行开始，每一行为一个面值，请输出最少携带的硬币。
用例：
  输入：
    80 3
    1
    18
    67
  输出：
    12
```

```Golang
func GetNum(n int, price []int, tmp []int) []int{
  num := 2^32
  for i:=0; i<len(price); i++{
    if (n-price[i] >= 0){
      if (tmp[n-price[i]]+1 < num){
        num = tmp[n-price[i]] + 1
      }
    }
  }
  if (len(tmp) <= n){
    tmp = append(tmp, num)
  }
  return tmp
}

func main(){
  //获取输入
  var n,m int
  var price []int
  fmt.Scan(&m)
  fmt.Scan(&n)
  for i:=0; i<n; i++ {
    x := 0
    fmt.Scan(&x)
    price = append(price, x)
  }
  sort.Ints(price)

  var tmp []int
  tmp = append(tmp, 0)

  for j:=1; j<=m; j++ {
    tmp = GetNum(j, price, tmp)
  }
  fmt.Println(tmp[m])
}
```

呼~长舒一口气

#### 参考资料

* [什么是动态规划？动态规划的意义是什么？](https://www.zhihu.com/question/23995189/answer/613096905)
* [使用斐波那契数列引入了动态规划的概念](https://www.cnblogs.com/liweiwei1419/p/8616113.html)
* [动态规划 - Wikipedia](https://zh.wikipedia.org/wiki/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92)