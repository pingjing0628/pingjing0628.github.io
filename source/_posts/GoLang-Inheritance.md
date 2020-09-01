---
title: GoLang-Inheritance
date: 2019-12-08 01:21:15
tags: 
  - GoLang
  - Inheritance
categories: 
  - GoLang
---

## Inheritance

<!--more-->
---

```go=
package main

import (
	"fmt"
)

type People struct {
	name  string
	age   int
}

func (people People) Hello() string {
	return `Hi! I am ` + people.name
}

func main() {
	yu := People{`Yu`, 18}

	fmt.Println(yu.Hello()) // Hi! Someone, I am Yu
}
```

* 假設想要有新的結構是 `Taiwanese` 繼承 `People` ，寫法是這樣的：
```go=
package main

import (
	"fmt"
)

type People struct {
	name  string
	age   int
}

type Taiwanese struct {
	People
	country  string
}

func (people People) Hello() string {
	return `Hi! I am ` + people.name
}

func main() {
	yu := Taiwanese{People{`Yu`, 18}, `Taiwan`}

	fmt.Println(yu)         // {{Yu 18} Taiwan}
	fmt.Println(yu.country) // Taiwan

	fmt.Println(yu.name)    // Yu
	fmt.Println(yu.age)     // 18
	fmt.Println(yu.Hello()) // Hi! I am Yu

	// 指定結構
	fmt.Println(yu.People.name)    // Yu
	fmt.Println(yu.People.age)     // 18
	fmt.Println(yu.People.Hello()) // Hi! I am Yu
}
```

* 可以多重繼承，但若成員重覆的話，就會出現 `ambiguous selector` 編譯錯誤
```go=
package main

import (
	"fmt"
)

type People struct {
	name  string
	age   int
}

type Animal struct {
	name  string
}

type Taiwanese struct {
	People
	Animal
	country  string
}
```

### 覆寫成員與方法
* 第一個例子可以看到成員與方法是會被繼承下來的，也可以被覆寫：
```go=
package main

import (
	"fmt"
)

type People struct {
	name string
	age  int
}

type Taiwanese struct {
	People
	name    string
	country string
}

func (people People) Hello() string {
	return `Hi! I am ` + people.name
}

func (taiwanese Taiwanese) Hello() string {
	return `你好！我是` + taiwanese.name
}

func main() {
	yu := Taiwanese{People{`Yu`, 18}, `妤`, `Taiwan`}

	fmt.Println(yu.Hello())        // 你好！我是妤
	fmt.Println(yu.People.Hello()) // Hi! I am Yu
}

```

* `Taiwanese` 看起來很像 `People` 了，不過還是不能當作是 `People` 使用（多型），要使用 `interface` 之後才能解決。

