---
title: 斐波纳契数列
tags:
  - Go
  - 斐波那数列
categories:
  - Go 
  - 算法
---
> `斐波纳契数列`，又称黄金分割数列，指的是这样一个数列：1、1、2、3、5、8、13、21、……在数学上，斐波纳契数列以如下被以递归的方法定义：F0=0，F1=1，Fn=F(n-1)+F(n-2)（n>=2，n∈N*）



#### 递归实现
```go
package main

import "fmt"

func fibonacci(num int) int{
	if num<2{
		return 1
	}
	
	return fibonacci(num-1) + fibonacci(num-2)
}


func main(){
	for i := 0; i<10; i++{
		nums := fibonacci(i)
		fmt.Println(nums)
	}
}
```

#### 闭包实现
```go
package main

import "fmt"

func fibonacci() func() int{
	a, b := 0, 1
	return func() int{
		a, b = b, a+b
		return a
	}
}


func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}
```