---
title: GoByExample-变量
tags:
  - Go
  - GoByExamble
categories:
  - Go
top: false
date: 2022-07-24 18:33:18
---

# GoByExample-变量



### 1. 变量的来历

​		程序运行过程中的数据都是保存在内存中，我们想要在代码中操作某个数据时就需要去内存上找到这个变量，但是如果我们直接在代码中通过内存地址去操作变量的话，代码的可读性会非常差而且还容易出错，所以我们就利用变量将这个数据的内存地址保存起来，以后直接通过这个变量就能找到内存上对应的数据了。



### 2.  变量类型

​		变量（Variable）的功能是存储数据。不同的变量保存的数据类型可能会不一样。经过半个多世纪的发展，编程语言已经基本形成了一套固定的类型，常见变量的数据类型有：整型、浮点型、布尔型等。

Go语言中的每一个变量都有自己的类型，并且变量必须经过声明才能开始使用。



### 3. 变量声明

Go语言中的变量需要声明后才能使用，同一作用域内不支持重复声明。并且Go语言的变量声明后必须使用。

- 方法一：声明一个变量, 默认的值是 0

  ```go
  package main
  
  import "fmt"
  
  func main(){
      // 方法一：声明一个变量, 默认的值是0
      var a int
      fmt.Println("a = ", a)
      fmt.Printf("a的类型是: %T\n", a)
  }
  ```
  
- 方法二：声明一个变量, 并初始化一个值

  >  Go语言在声明变量的时候，会自动对变量对应的内存区域进行初始化操作。每个变量会被初始化成其类型的默认值，例如： 整型和浮点型变量的默认值为0。 字符串变量的默认值为空字符串。 布尔型变量默认为`false`。 切片、函数、指针变量的默认为`nil`。

  ```go
  package main
  
  import "fmt"
  
  func main(){
      // 方法二：声明一个变量, 初始化一个值
      var b int = 100
      fmt.Printf("b = %d, type of b = %T\n", b, b)
  
      var bb string = "google.com"
      fmt.Printf("bb = %s, bb的类型是: %T\n", bb, bb)
  }
  ```

- 方法三：有时候我们会将变量的类型省略，这个时候编译器会根据等号右边的值来推导变量的类型完成初始化。

  ```go
  package main
  
  import "fmt"
  
  func main(){
  
      // 方法三：有时候我们会将变量的类型省略，这个时候编译器会根据等号右边的值来推导变量的类型完成初始化。
      var c = 100
      fmt.Printf("c = %d, type of c = %T\n", c, c)
  
      var cc = "google.com"
      fmt.Printf("cc = %s, cc的类型是: %T\n", cc, cc)
  }
  ```
  
- 短声明，只能在函数内

  ```go
  package main
  
  import "fmt"
  
  func main(){
  
      // 方法四：(常用的方法) 省去var关键字，直接自动匹配
      // 注: 短声明是在函数或方法内部使用, 不支持全局变量声明！！！！
      e := 100
      fmt.Printf("e = %d, e的类型是: %T\n", e, e)
  
      f := "google.com"
      fmt.Printf("f = %s, f的类型是: %T\n", f, f)
  }
  ```
  
- 多变量声明

  ```go
  package main
  
  import "fmt"
  
  func main(){
  	// 声明多个变量
      var xx, yy int = 100, 200
      fmt.Println("xx = ", xx, ", yy = ", yy)
      var kk, jj = 300, "google.com"
      fmt.Println("kk = ", kk, ", jj = ", jj)
  
      var (
          nn int = 100
          mm bool = true
      )
      fmt.Println("nn = ", nn, ", mm = ", mm)
  }
  ```
