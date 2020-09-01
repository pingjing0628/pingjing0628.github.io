---
title: GoLang-Anonymous Function
date: 2019-12-03 22:56:36
tags: 
  - GoLang
  - 取址
  - Closure
categories: 
  - GoLang
---

## Anonymous Function

<!--more-->
---

* 匿名函式的使用方法
```go=
package main

import "fmt"

func main() {
	addFunc := func(a, b int) int {
		return a + b
	}

	sum := addFunc(1, 2)

	fmt.Println(sum)    // 3
}
```

* 若覺得還要存放一個變數太麻煩，這也可以省略
```go=
package main

import "fmt"

func main() {
	sum := func(a, b int) int {
		return a + b
	}(1, 2)

	fmt.Println(sum)    // 3
}

```

* 想要從另一個函式取得匿名函式
```go=
package main

import "fmt"

func getFunc() func(a, b int) int {
	return func(a, b int) int {
		return a + b
	}
}

func main() {
	sum := getFunc()(1, 2)

	fmt.Println(sum) // 3
}
```

* 組合技
```go=
package main

import "fmt"

func getFunc() func(a, b int) int {
	return func(a, b int) int {
		return func(a, b int) int {
			return a + b
		}(a, b)
	}
}

func main() {
	sum := getFunc()(1, 2)

	fmt.Println(sum) // 3
}
```

### Closure
* 閉包是指，變數被關在某個區塊內
```go=
package main

import "fmt"

func getFunc() func(a, b int) int {
	base := 10

	return func(a, b int) int {
		return base + func(a, b int) int {
			return a + b
		}(a, b)
	}
}

func main() {
	sum := getFunc()(1, 2)

	fmt.Println(sum) // 13
}
```
```go=
package main

import "fmt"

func getFunc() func(a, b int) int {
	base := 10

	return func(a, b int) int {
		return func(a, b int) int {
			return base + a + b
		}(a, b)
	}
}

func main() {
	sum := getFunc()(1, 2)

	fmt.Println(sum) // 13
}
```

* 雖然匿名函式內容是封閉的，但 `base` 變數卻能被關進匿名函式裡，甚至是「匿名函式的匿名函式裡」，這就是閉包的特性。
* 因為 Go 有取址運算，我們能拿得到變數真正的位址。
```go=
package main

import "fmt"

func getFunc() func(a, b int) int {
	base := 10
	fmt.Printf("In getFunc()   %p = %d\n", &base, base)

	return func(a, b int) int {
		fmt.Printf("In getFunc() closure   %p = %d\n", &base, base)

		return func(a, b int) int {
			fmt.Printf("In getFunc() closure's closure   %p = %d\n", &base, base)

			return base + a + b
		}(a, b)
	}
}
// &是取位址

func main() {
	sum := getFunc()(1, 2)

	fmt.Println(sum) // 13
}

```
* 最後輸出：
```go=
In getFunc()   0xc420010058 = 10
In getFunc() closure   0xc420010058 = 10
In getFunc() closure's closure   0xc420010058 = 10
13
```

* 在三個地方的 `base` 變數位址都是 `0xc420010058` ，這是「變數被關在某個區塊內」所代表的意思。
