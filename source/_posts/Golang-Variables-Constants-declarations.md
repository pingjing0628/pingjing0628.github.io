---
title: Golang-Variables & Constants declarations
date: 2019-12-01 22:18:18
tags: 
  - GoLang
categories: GoLang
---

## 變數與常數的宣告

<!--more-->
---

### 變數宣告
* 宣告變數使用 `var`，以下宣告num 變數為int 型態，給予初始值 `10`
```go=
var num int = 10
```

* `int`可以省略
```go=
var num = 10
```

* 若不給初始值，預定義變數都會有預設值，Go語言稱之為零值(The zero value)， `int`的零值是0，所以下面兩行宣告是等價的
```go=
var num int = 0
var num int
```

* Go可以一次宣告多個變數，下面的型態分別會推斷為 `string`、`int`、`float64`
```go=
var name, age, height = "Miles", 18, 169.9
```

* 也可分多行宣告
```go=
var (
	name = "Miles"
	age = 18
	height = 169.9
)
```

* 多行宣告並指定型態與指定初始值
```go=
var (
	name string = "Miles"
	age uint = 18
	height float32 = 169.9
)
```

* 多行宣告定指定型態不指定初始值
```go=
var (
	name string
	age uint
	height float32
)
```

### 短變數宣告
* 在func裡，若要宣告變數同時指定初值，可以使用**短變數宣告**
```go=
name := "Miles"
age := 18
height := 169.9
```

>就如同 PHP 的 $name = 'Miles' 一樣，宣告變數同時給值

* 一樣可以寫成一行
```go=
name, age, height := "Miles", 18, 169.9
```

### 常數宣告
* 宣告變數使用 const 關鍵字，下面宣告了 num 變數為 int 型態，並給值為 10：
```go=
const num int = 10
```

* num 會是不可變的常數，試圖指定新值會在編譯時報錯
```go=
const num int = 10

// Error
num = 20
```

* 除了常數一定要給值，其他宣告的方法都跟變數一樣，如一次宣告多個常數
```go=
const name, age, height = "Miles", 18, 169.9
```

* 多行宣告
```go=
const (
	name = "Miles"
	age = 18
	height = 169.9
)
```

* 多行宣告並指定型態
```go=
const (
	name string = "Miles"
	age uint = 18
	height float32 = 169.9
)
```

* 如果宣告了一個 num 變數沒使用， Go 會在編譯時期出錯：
```go=
./hello.go:8:6: num declared and not used
```

* 常數則可以宣告但不使用。