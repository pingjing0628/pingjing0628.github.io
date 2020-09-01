---
title: GoLang-First class function
date: 2019-12-03 22:48:22
tags: 
  - GoLang
  - type
categories: 
  - GoLang
---

## First class function

<!--more-->
---

```go=
package main

import "fmt"
import "reflect"

func add(a, b int) int {
	return a + b
}

func main() {
	add2 := add

	fmt.Println(add(1, 2))               // 3
	fmt.Println(add2(1, 2))              // 3
	fmt.Println(reflect.TypeOf(add))     // func(int, int) int
	fmt.Println(reflect.TypeOf(add2))    // func(int, int) int
}
```

* `add` 函式也可以當作一個變數來操作，熟悉 Javascript 一定對這個寫法不陌生。
* 定義一個變數是函式型態（Function Type）：
```go=
package main

import "fmt"
import "reflect"

func add(a, b int) int {
	return a + b
}

func main() {
	var add2 func(int, int) int

	fmt.Println(add2)          // <nil>

	add2 = add

	fmt.Println(add(1, 2))     // 3
	fmt.Println(add2(1, 2))    // 3
}
```

* 因為是變數，所以也可成為其他函式的傳入值，就像 Javascript callback 一般：
```go=
package main

import "fmt"

func cmd(a, b int, callback func(int, int) int) int {
	return callback(a, b)
}

func add(a, b int) int {
	return a + b
}

func main() {
	var add2 func(int, int) int

	add2 = add

	fmt.Println(cmd(1, 2, add))      // 3
	fmt.Println(cmd(1, 2, add2))     // 3
}

```


* `func(int, int) int` 會覺得很冗長，可以使用 `type` 來定義新的型態：
```go=
package main

import "fmt"

type addFunc func(int, int) int

func cmd(a, b int, callback addFunc) int {
	return callback(a, b)
}

func add(a, b int) int {
	return a + b
}

func main() {
	var add2 addFunc

	add2 = add

	fmt.Println(cmd(1, 2, add))      // 3
	fmt.Println(cmd(1, 2, add2))     // 3
}

```

### 匿名函式
* 除了直接宣告函式傳入之外，也可以使用匿名函式：
```go=
package main

import "fmt"

type addFunc func(int, int) int

func cmd(a, b int, callback addFunc) int {
	return callback(a, b)
}

func main() {
	var add2 addFunc

	add2 = func(a, b int) int {
		return a + b
	}

	fmt.Println(cmd(1, 2, add2))     // 3
}

```
* 或者直接 inline 會更簡潔：
```go=
package main

import "fmt"

type addFunc func(int, int) int

func cmd(a, b int, callback addFunc) int {
	return callback(a, b)
}

func main() {

    // 3
	fmt.Println(cmd(1, 2, func(a, b int) int {
        return a + b
    }))
}

```