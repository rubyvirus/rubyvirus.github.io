---
title: golang基础gorountine知识
tag: go
date: 2018-11-04
---

# GO 并发（Channel）

听说 GO 性能方面非常牛逼，记录一下学习过程。

> 注意  一个概念

并发不是并行： 并发是通过时间切片实现的，并行则是利用计算机的多核功能

1. GO 性能设计

GORountine 奉行通过通信来共享内存，区别于其它通过共享内存来共享通信

2. 来几个  简单例子

#### 第一个例子是匿名函数进行 gorountine

```
package main

import (
	"fmt"
)

func main() {
	c := make(chan bool)
	go func() {
		fmt.Println("gogogo")
		c <- true
	}()
	fmt.Println(c)
	<-c
}
```

#### 第二个例子是需要关闭chan的情况

```
package main

import (
	"fmt"
)

func main() {
	c := make(chan bool)
	go func() {
		fmt.Println("gogogo")
		c <- true
		close(c)
	}()

	for v := range c {
		fmt.Println(v)
	}
}
```

#### 第二个例子是利用多核展示并行

```
package main

import (
	"fmt"
	"runtime"
)

func main() {
	runtime.GOMAXPROCS(2)
	c := make(chan bool, 10)
	for index := 0; index < 10; index++ {
		go Go(c, index)
	}
	for index := 0; index < 10; index++ {
		<-c
	}
}

func Go(c chan bool, index int) {
	a := 1
	for index := 0; index < 100000; index++ {
		a += index
	}
	fmt.Println(index, a)
	c <- true
}

```
