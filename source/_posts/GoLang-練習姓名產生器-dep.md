---
title: GoLang-練習姓名產生器-dep
date: 2019-12-09 00:13:35
tags: 
  - GoLang
categories: 
  - GoLang
---

## Practice

<!--more-->
---

* 透過應用程式可以產生姓名，並建立屬於自己的字典，讓取名可以有自定義的方向，還可避開不喜歡的字。


### 使用dep

* 使用 `dep` 初始化一個 `CLI` 專案。
* `$ brew install dep`
* 找現有程式參考。 `Dapper` 是個不錯且夠簡單的 `CLI` 專案，其 `main.go` 用了 [urfave/cli][] （有改名過）框架。

* 先下載 `cli` 套件原始碼：
`$ go get github.com/urfave/cli`
* 建立主要檔案 `main.go`
```go=
package main

import (
	"os"
	"github.com/urfave/cli"
)

func main() {
	app := cli.NewApp()
	app.Name = `Namer`
	app.Run(os.Args)
}
```

* 切記，須將project建立在 `$HOME/go/src/myproject`

* 關於 `dep` 的應用
```
$ dep
dep is a tool for managing dependencies for Go projects

Usage: dep <command>

Commands:

  init     Initialize a new project with manifest and lock files
  status   Report the status of the project's dependencies
  ensure   Ensure a dependency is safely vendored in the project
  prune    Prune the vendor tree of unused packages
  version  Show the dep version information

Examples:
  dep init                               set up a new project
  dep ensure                             install the project's dependencies
  dep ensure -update                     update the locked versions of all dependencies
  dep ensure -add github.com/pkg/errors  add a dependency to the project

Use "dep help [command]" for more information about a command.

```

* 需至`$HOME/go/src/myproject` 進行`dep init`
```
$ dep init
  Using ^1.20.0 as constraint for direct dep github.com/urfave/cli
  Locking in v1.20.0 (cfb3883) for direct dep github.com/urfave/cli
```

* 將程式碼裡的依賴都找出，將其下載到 `vendor` 目錄。
* `vendor` 須不須 `commit` ，可以參考官方文件，是隨意，同時也很負責的說明了優缺點。

```
$ go run main.go
NAME:
   Namer - A new cli application

USAGE:
   main [global options] command [command options] [arguments...]

VERSION:
   0.0.0

COMMANDS:
     help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h     show help
   --version, -v  print the version
```