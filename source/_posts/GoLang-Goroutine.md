---
title: GoLang-Goroutine
date: 2020-09-07 11:55:53
tags: 
  - GoLang
  - Goroutine
categories: 
  - GoLang
---

## Goroutine

<!--more-->
---

Goroutine 像是 Go 的 thread, 使 Go 建立多工處理，搭配 Channel 使 Goroutine 操作簡單化
程式啟動時只有呼叫 main 函式的 goroutine，因此稱為 <b>主</b> goroutine

# Single thread
每行程式碼都依序執行

```golang=
func main() {
    reply("Ni")
    reply("How")
}

func reply(s string) {
    for i := 0; i < 5; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
}
```
Output:
```
Ni
Ni
Ni
Ni
How
How
How
How
How
```
![](single_thread.JPG)