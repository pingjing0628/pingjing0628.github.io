---
title: GoLang-Struct
date: 2019-12-03 23:29:00
tags: 
  - GoLang
  - 指標
  - type
  - new
categories: 
  - GoLang
---

## Struct

<!--more-->
---

* `struct` 是定義資料的集合，跟物件很像，也是把資料集合在一包。

### 定義
* `struct` 可以使用 `type` 關鍵字定義，開頭的大小寫跟函式一樣，會影響能見度；內部的成員名的定義也是一樣。
```go=
package main

import "fmt"

type People struct {
	name string
	age  int
}

func main() {
	yu := People{`Yu`, 18}
	fmt.Println(yu)       // {Yu 18}
	fmt.Println(yu.name)  // Yu
	fmt.Println(yu.age)   // 18
	
	wang := People{age: 18, name: `Wang`}
	fmt.Println(wang)        // {Wang 18}

	part := People{name: `Part`}
	fmt.Println(part)        // {Part }

	empty := People{}
	fmt.Println(empty)       // { 0}

	var empty2 People
	fmt.Println(empty2)      // { 0}
}
```

* `wang` 範例可以在指定值的時候改順序
* `part` 範例在指定部分值的時候，必須確定指明是哪個部分，比方說上例的 `name` ，即使順序一樣，沒有指明 `name` 的話一樣會出錯，比方說： `part := People{"Part"}` 這是不合法的範例
* `empty` 、 `empty2` 是零值範例

* 如果把一個結構指定給另一個結構時，它會使用複製：
```go=
package main

import "fmt"

type People struct {
	name string
	age  int
}

func main() {
	yu := People{`Yu`, 18}
	copy := yu
	
	copy.name = `Copy`
	
	fmt.Println(yu) //  {Yu 18}
	fmt.Println(copy) //   {Copy 18}
}

```

* 函數傳遞也是如此，如果需要傳址，可以直接用指標
```go=
package main

import "fmt"

type People struct {
	name string
	age  int
}

func changeName(people *People) {
	people.name = `Someone`
}

func main() {
	yu := People{`Yu`, 18}
	fmt.Println(yu)  //   {Yu 18}

	changeName(&yu)
	fmt.Println(yu)  //   {Someone 18}
}

```

* 使用 `new` 關鍵字的話，得到的會是指標：
```go=
package main

import "fmt"

type People struct {
	name string
	age  int
}

func changeName(people *People) {
	people.name = `Someone`
}

func main() {
	yu := new(People)
	yu.name = `Yu`
	yu.age = 18
	fmt.Println(yu)  //   &{Yu 18}

	changeName(yu)
	fmt.Println(yu)  //   &{Someone 18}
}

```

* 結構的成員也可以結構，雖然無法直接把自己包起來，但可以使用指標，如：
```go=
package main

import "fmt"

type People struct {
	name   string
	age    int
	friend *People
}

func main() {
	friend := new(People)
	friend.name = `Friend`

	yu := People{`Yu`, 18, friend}
	fmt.Println(yu)               // &{Yu 18 0xc42000a060}
	fmt.Println(yu.name)          // Yu
	fmt.Println(yu.friend.name)   // Friend
}

```