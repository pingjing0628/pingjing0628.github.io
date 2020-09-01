---
title: GoLang-語法筆記整理
date: 2019-12-15 23:13:33
tags: 
  - GoLang
categories: 
  - GoLang
---

## 整理

<!--more-->
---

1. `go build yu.go`
建立exe檔案
`./yu`
執行檔案

2. `go run main.go`
於commandline 執行檔案做測試

3. `go doc fmt`
查看套件用法
`go doc fmt.Println`
查看此套件中的方法用法

4. `const earthsGravity = 9.80665`
定義常數變數

5. `amount, unit := 10, "doll hairs"`
可以同時定義兩個變數且不同型態

6. 空字串、空數字
```go=
package main
import "fmt"

func main() {
  var emptyString string
  var emptyInteger integer

  fmt.Println(emptyString)
  fmt.Println(emptyInteger)

  // 
  // 0
}
```


7. `Println` vs `Print`
```go=
package main

import "fmt"

func main() {
  fmt.Println("Let's first see how", "the Println() method works.")
  fmt.Println("There's also a default space")
  
  fmt.Print("Print", "is", "different")
  fmt.Print("See?")

  /*
    Let's first see how the Println() method works.
    There's also a default space between the string arguments.
    PrintisdifferentSee?
  */

}
```

8. 使用 `%v`, use `Printf`
```go=
package main

import "fmt"

func main() {
  animal1 := "cat"
  animal2 := "dog"
  
  fmt.Printf("Are you a %v or a %v person?", animal1, animal2)

  // Are you a cat or a dog person?
}

```

9. `%T` is Type
10. `%d` is integer
11. `%f` is float, ex: 3.500000
12. `%.2f` is float, ex: 3.50 
13. 使用`Sprintln` 帶入參數並利用 `Println`印出字串
```go=
package main

import "fmt"

func main() {
  step1 := "Breathe in..."
  step2 := "Breathe out..."
  
  fmt.Sprintln(step1, step2)
  
}
  // doesn't print anything

```
```go=
package main

import "fmt"

func main() {
  step1 := "Breathe in..."
  step2 := "Breathe out..."
  
  meditation := fmt.Sprint(step1, step2)
  fmt.Println(meditation)
}
  // Breathe in...Breathe out...

```

14. `.Scan()` to get user input
```go=
package main

import "fmt"

func main() {
  fmt.Println("What would you like for lunch?")
  
  var food string
  fmt.Scan(&food)
  
  fmt.Printf("Sure, we can have %v for lunch.", food)
}

```

15. random number
```go=
package main

import (
	"fmt"
  "math/rand"
  "time"
)

func main() {
  // Add your code below:
  rand.Seed(time.Now().UnixNano())
  
  amountLeft := rand.Intn(10000)
  
  fmt.Println("amountLeft is: ", amountLeft)
  
	if amountLeft > 5000 {
		fmt.Println("What should I spend this on?")
  } else {
    fmt.Println("Where did all my money go?")
  }
}

// amountLeft is:  7298
// What should I spend this on?

```

16. **address** use `&`
```go=
package main

import "fmt"

func main() {
	treasure := "The friends we make along the way."
	
    fmt.Println(&treasure)

    // 0xc0000101e0
}

```

17. **Pointers** are variables that specifically store addresses.
`var pointerForInt *int`
`pointerForInt` will store the address of a variable that has an `int` data type. 

```go=
lyrics := "Moments so dear" 
pointerForStr := &lyrics

*pointerForStr = "Journeys to plan" 

fmt.Println(lyrics) // Prints: Journeys to plan
```

```go=
func addHundred(num int) {
  num += 100
}

func main() {
  x := 1
  addHundred(x)
  fmt.Println(x) // Prints 1
}
```

```go=
func addHundred (numPtr *int) {
  *numPtr += 100
}

func main() {
  x := 1
  addHundred(&x)
  fmt.Println(x) // Prints 101
}
```