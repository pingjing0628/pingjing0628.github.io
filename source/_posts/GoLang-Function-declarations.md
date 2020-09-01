---
title: GoLang-Function declarations
date: 2019-12-02 23:57:34
tags: 
  - GoLang
  - 單一職責原則
categories: 
  - GoLang
---

## Function declarations

<!--more-->
---

### 定義函式
```go=
func <funcName>([param1 type1[, param2 type2 ...]]) ([return1 type1[, return2 type2 ...]]) {
	
	// Do something
	
    return [value1[, value2 ...]]
}

```

* `funcName` 為 `function` 名稱，首字大寫為 `public` 是外部套件可以共用的，小寫為 `private` 只有套件內部可以看得到。
* `param` 為傳入值。
* `return` 為回傳值。 Go 可以定義多個回傳值，而且還能定義它的名稱。定義名稱時，回傳的用法會有點特別。
```go=
package main

import "fmt"

func add(a, b int) (int, bool) {
	return a + b, true
}
// add is a private function name

func main() {
	sum, ok := add(1, 2)

	fmt.Println(sum)    // 3
	fmt.Println(ok)     // true
}
```
* 有點類似 `Map` 取值的用法

* 若有定義名稱的話， `return` 會把當下兩個變數的內容回傳出去。
```go=
package main

import "fmt"

func add(a, b int) (sum int, ok bool) {
	sum = a + b
	ok = true 
	return
}

func main() {
	sum, ok := add(1, 2)

	fmt.Println(sum)    // 3
	fmt.Println(ok)     // true
}
```

* 許多程式在一開始就定義 result ，在 return 的時候回傳出去。 Go 則是在規格上直接實作出來
* 傳回多個值時，必須照順序接值；若不需要回傳值，可以用 `_` 略過：
```go=
package main

import "fmt"

func add(a, b int) (sum int, ok bool) {
	sum = a + b
	ok = true 
	return
}

func main() {
	_, ok := add(1, 2)

	fmt.Println(ok)     // true
}
```

* 傳回多值通常會用在錯誤處理，若使用不恰當的話，容易違反**單一職責原則**。
* 若傳入值是不定的，可以用 `...` 來表示
```go=
package main

import "fmt"

func add(numbers ...int) (sum int) {
	sum = 0
	for _, num := range numbers {
		sum += num
	}

	return
}

// _為忽略index

func main() {
	sum := add(1, 2, 3, 4, 5)

	fmt.Println(sum)     // 15
}
```
* 裡面的 `numbers` 型態會是 Slice `[]int` ，所以可以用 `for range` 迭代。
