---
title: GoLang-gomodule
date: 2020-01-05 00:03:08
tags: 
  - GoLang
  - Gomodule
  - Govendor
categories: 
  - GoLang
---

## GoModule

<!--more-->
---

先建立一個範例檔
```golang=
package main

import (
	"pingjing/example/first"
)

func main() {
	first.Printfirst()
}

```

### 使用傳統govendor的做法
**govendor** 是一種vendor管理，是一個PackageManagementTools

`govendor init`
`govendor fetch pingjing/example/first`
用`go build -v -o main .` 產生 main binary檔案
若不在 GOPATH 裡面工作，就會遇到錯誤訊息 -> 使用go module

---

### 使用go module 
用 **go module** 解決兩個問題
1. 專案內不必再使用 vendor 管理套件
2. 可任意 clone 專案到任何地方，直接下 `go build` 就可拿到執行檔

`export GO111MODULE=on` 啟動module變數
`go mod init 專案目錄` 專案會多一個 `go.mod` 檔案，用來記錄使用到的套件版本，

> example: 專案目錄 `Users/pingjing/docker/goPractice`
於程式碼 import時 需使用此路徑

若已在使用 `vendor` 管理， 則 `mod init` 會自動將 `vendor` 紀錄的版本寫入到 `go.mod`
`go mod download` 專案內會多 `go.sum` 檔案，在專案內下 `go build|test|install`，會自動將 `pkg` 下載到 `GOPATH/pkg/mod` 內
`go clean -i -x -modcache` 將 pkg 目錄清空

`Go build -v -o main` 產生 main binary檔案

> 記得 package 後加上的是 folder 名稱
---

### REFERENCES
* [Go 語言 1.11 版本推出 go module](https://blog.wu-boy.com/2018/10/go-1-11-support-go-module/)