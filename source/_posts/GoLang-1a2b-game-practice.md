---
title: GoLang-1a2b game practice
date: 2020-01-04 16:37:11
tags: 
  - GoLang
  - make
  - rand
  - append
  - 指標
categories: 
  - GoLang
---

## 1a2b Game practice

<!--more-->
---

### 說明
第13行 `random := make([]int, length)`
`make` 為初始化一個型態為int且長度為length的array
第73-77行 
```golang=
for input_int%10 != 0 {
    input = append([]int{input_int % 10}, input...)
    input_int = input_int / 10
}
```
(...)append會將其當作是slice，並進行兩個slice的拼接


完整程式碼如下：
```golang=
package main

import (
	"fmt"
	"math/rand"
	"os"
	"time"
)

// generate a random number
func generate_random_num(length int) []int {
    initial := []int{0, 1, 2, 3, 4, 5, 6, 7, 8, 9} // create an array 0-9
    random := make([]int, length)                  // 初始化一個長度為輸入數字長度的陣列
    if length > 10 || length < 0 {
	    fmt.Println("invalid length:", length)
        os.Exit(-1) // invalid and pop out
    }
    r := rand.New(rand.NewSource(time.Now().UnixNano())) // create and seed the generate random a number

    for i := 0; i < length; i++ {
	    tmp := r.Intn(len(initial) - i) // Intn() return int, a non-negative random number

	    random[i] = initial[tmp] // initial 的陣列[2,0,0,0] 
        initial[tmp], initial[len(initial)-1-i] = initial[len(initial)-1-i], initial[tmp]
	    // swap，將initial陣列中的tmp值與長度遞減的值交換位置，為避免接下來在取得相同的值
    }
    return random
}

// mapping the input value and random value
func mapping_num(input, random []int) bool {
    var A, B int

    if len(input) != len(random) { // if length is different then return false
        fmt.Println("Number you entered didn't match the number you set it!")
        return false
    }
    for i := 0; i < len(input); i++ {
        if input[i] == random[i] {
		    A++
        }
        for j := 0; j < len(input); j++ {
            if input[i] == random[j] {
                B++
            }
        }
    }
    B -= A
    fmt.Printf("%dA%dB\n", A, B)

    if A == len(input) {
        fmt.Println("You Win!")
        return true
    }
    return false
}

func main() {
    var input_int, length int
    var input []int
    status := false

    fmt.Println("Welcome to the Final Code Game!")
    fmt.Println("Please entering the number you want to guess!")
    fmt.Scanf("%d", &length)

    random_num := generate_random_num(length)

    for !status {
        fmt.Printf("Please input numbers\n")
        fmt.Scanf("%d", &input_int)

        for input_int%10 != 0 {
            input = append([]int{input_int % 10}, input...)
            //加上(...)append會將其當作是slice，並進行兩個slice的拼接
            input_int = input_int / 10
        }

        status = mapping_num(input, random_num)

        if !status { // if status is false, then clear input
            input = nil
        }
    }
}

```