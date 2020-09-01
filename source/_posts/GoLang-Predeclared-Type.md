---
title: GoLang-Predeclared Type
date: 2019-12-01 22:47:49
tags: 
  - GoLang
categories: GoLang
---

## Predeclared Type

<!--more-->
---

* Go 的資料型別有 11 種，其中 Predeclared Type，他們是「有名稱的型態（Named Type）」。

### Boolean types
* 最簡單的型別 `bool` ，只有兩個預定義的常數 `true` 和 `false` 。

### Numeric types
* numeric 型態包含了 `integer` （整數）、 `float`（浮點數）、 `complex`（複數）三種。
* 整數又分帶號 `int` 與不帶號 `uint` 兩類，也可以直接指定大小 `int8` 、 `int16` 、 `int32` 、 `int64` 或是不帶號的 `uint8` 、 `uint16` 、 `uint32` 、 `uint64` ，這些相信一看就知道佔了多少容量（bit
* 至於 `int` 和 `uint` 會使用哪一個要看平台實作決定，有可能是 32 bit 也有可能是 64 bit。
* 而另外還有兩個整數型態： `rune` 是 `int32` 的別名， `byte` 是 `int8` 的別名。
* `float` 有 `float32` 與 `float64` 兩種，但沒有 `float` 。
* complex 則表示複數，以 RE + IMi 的方法表示，如：
```go=
10+5i
```

* 大小則有分 `complex64` 與 `complex128` 兩種。
* Go 內建的 `math` 套件提供常數取得各型態的最大值和最小值，除了解整數範圍外，也有助於實作上的判斷。
```go=
package main

import "fmt"
import "math"

func main() {
	fmt.Println(math.MinInt8)
	fmt.Println(math.MaxInt8)
	fmt.Println(math.MinInt16)
	fmt.Println(math.MaxInt16)
	fmt.Println(math.MinInt32)
	fmt.Println(math.MaxInt32)
	fmt.Println(math.MinInt64)
	fmt.Println(math.MaxInt64)
	
	// Uint 最小值是 0
	fmt.Println(math.MaxUint8)
	fmt.Println(math.MaxUint16)
	fmt.Println(math.MaxUint32)
	fmt.Println(math.MaxUint64)
	
	// Float 的表示是最小非 0 浮點數
	fmt.Println(math.SmallestNonzeroFloat32)
	fmt.Println(math.MaxFloat32)
	fmt.Println(math.SmallestNonzeroFloat64)
	fmt.Println(math.MaxFloat64)
}
```

* 注意，不同的數字型態，是不能直接摻在一起操作的。如 `int8` 不能跟 `uint8` 相加。
* `int` 有可能是 32 位元，但 `int` 也不能跟 int32 相加。


### String types
* Go 語言字串都是 UTF-8 字元集編碼，它可以正常的處理多國語言。字串可以使用雙引號 " 或反引號 \` 定義，也可以相加

```go=
package main

import "fmt"

func main() {
	fmt.Println("Hello 雙引號")
	fmt.Println(`Hello 反引號`)
	fmt.Println("雙引號" + `反引號`)
}
```
