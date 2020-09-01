---
title: GoLang-Map Type
date: 2019-12-02 23:31:21
tags: 
  - GoLang
  - Map
categories: 
  - GoLang
---

## Map Type

<!--more-->
---

* 許多語言都提供 `key-value` 存放方法的 `map` 結構， Go 使用內建型態 `map` 實作。
* `map` 型態表示方法為： `map[keyType]valueType` ， `map` 是關鍵字， `keyType` 必須是可比較（Comparable）的型態，如 string 、 int 等， `valueType` 則是內容形態。

### 建立
* 建立 `Map` 資料型態也是用 `make` ，設定與取值的方法跟大部分的語言（如 PHP ）很像
```go=
package main

import "fmt"

func main() {
	score := make(map[string]int)

	fmt.Println(score)  // map[]
	
	score["Yu"] = 80
	score["Wang"] = 60
	
	fmt.Println(score)          // map[Yu:80 Wang:60]
	fmt.Println(score["Yu"]) // 80
	fmt.Println(score["Wang"])  // 60
}

```

* 如果有初值的話，設定的方法很像 JSON
```go=
package main

import "fmt"

func main() {
	score := map[string]int{
		"Yu": 80,
		"Wang": 60,
	}

	fmt.Println(score)  // map[]

	score["Yu"] = 80
	score["Wang"] = 60

	fmt.Println(score)          // map[Yu:80 Wang:60]
	fmt.Println(score["Yu"]) // 80
	fmt.Println(score["Wang"])  // 60
}
```

* 宣告值最後一行 `"Wang": 60,` 的逗號是必要要加的。

* 這個寫法如果不給初值的話，就會跟使用 `make` 方法結果一樣：
```go=
score := make(map[string]int)

score := map[string]int{}
```

* `map` 跟 `slice` 一樣是使用參考
```go=
package main

import "fmt"

func main() {
	score := map[string]int{
		"Yu": 80,
		"Wang": 60,
	}

	ref := score

	fmt.Println(score)          // map[Yu:80 Wang:60]
	fmt.Println(ref)            // map[Yu:80 Wang:60]

	score["Someone"] = 0

	fmt.Println(score)          // map[Wang:60 Someone:0 Yu:80]
	fmt.Println(ref)            // map[Someone:0 Yu:80 Wang:60]
}

```

>它的順序應該是不固定的。


### 操作
* 取值使用 `[]` 指定 `key` 
* 會回傳兩個值，如果 `key` 存在，會回傳值與 `true`
* `key` 不存在則回傳零值與 `false`
```go=
package main

import "fmt"

func main() {
	score := map[string]int{
		"Yu": 80,
		"Wang": 60,
	}

	var value int
	var ok bool

    value, ok = score["Yu"]

	fmt.Println(value)     // 80
	fmt.Println(ok)        // true

    value, ok = score["Nobody"]

	fmt.Println(value)     // 0
	fmt.Println(ok)        // false
}
```

* 移除 `key` 使用 `delete`函式：
```go=
package main

import "fmt"

func main() {
	score := map[string]int{
		"Yu": 80,
	}

	var value int
	var ok bool

    value, ok = score["Yu"]

	fmt.Println(value)     // 80
	fmt.Println(ok)        // true

    delete(score, "Yu")
	value, ok = score["Yu"]

	fmt.Println(value)     // 0
	fmt.Println(ok)        // false
}

```

