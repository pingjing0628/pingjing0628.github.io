---
title: GoLang - Environment
date: 2019-11-30 23:12:58
tags: 
  - GoLang
categories: GoLang
---

## 安裝主程式

<!-- more --> 
***

* 主程式是指 go 指令，它能處理編譯、直譯、建置、格式化程式碼、測試、下載依賴等多種工具的組合。

### MacOS

```
$ brew install go
$ go version
go version go1.9.2 darwin/amd64
```

### 環境變數設定

* 可下 `go env` 取得環境變數：

```
$ go env
GO111MODULE=""
GOARCH="amd64"
GOBIN=""
GOCACHE="/Users/pingjing/Library/Caches/go-build"
GOENV="/Users/pingjing/Library/Application Support/go/env"
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="darwin"
GONOPROXY=""
GONOSUMDB=""
GOOS="darwin"
GOPATH="/Users/pingjing/go"
GOPRIVATE=""
GOPROXY="https://proxy.golang.org,direct"
GOROOT="/usr/local/Cellar/go/1.13.4/libexec"
GOSUMDB="sum.golang.org"
GOTMPDIR=""
GOTOOLDIR="/usr/local/Cellar/go/1.13.4/libexec/pkg/tool/darwin_amd64"
GCCGO="gccgo"
AR="ar"
CC="clang"
CXX="clang++"
CGO_ENABLED="1"
GOMOD=""
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=/var/folders/2l/n_g57dsd76n0xvwjmnx5xf6h0000gn/T/go-build181465848=/tmp/go-build -gno-record-gcc-switches -fno-common"
```

* 其中 `GOPATH` ，是需要設定的環境變數。
* 它代表著 go 程式的工作空間（workspace）， Windows 預設會設定在 `~\Go` ， Unix-like 則沒有預設，官方建議設定在 `~/go` 。

* `Workspace` 裡，劃分成三個主要目錄：
	* src - 原始碼
	* pkg - go package
	* bin - 編譯好的執行檔，有需要也可以加入 PATH 環境變數

* 接著使用 go 的第一個指令 `go get` ，它會把目標下載回來放在 `src` 裡，如：
` go get github.com/MilesChou/book-start-golang-30-days`
* 這樣會把上面這個 repo ，使用 HTTPS 協定 clone 到硬碟裡。

* 當編譯需要第三方的原始碼時，即可使用 go get 下載，同時這也可以用來下載自己或是第三方的原始碼。把所有原始碼集中成一個大大的 workspace ，這就是 go 管理原始碼的概念。


### Hello World

```go=
package main

import "fmt"

func main() {
	fmt.Println("Hello, 世界")
}
```

* 讓程式在建好的環境執行，先建個目錄，檔案名叫 `main.go`，切換到目錄後，將以上的內容輸入到檔案內，接著 `go run main.go`
```
$ mkdir -p /path/to/helloworld
$ cd /path/to/helloworld
# 輸入程式碼
$ vim main.go
# 執行程式碼
$ go run main.go
Hello, 世界
```

### 解釋

**go run**
```
$ go run main.go
Hello, 世界
```

* `go run` 所做的事正是直譯，也就是直接拿原始碼編譯，同時執行。

**package**
```
package main
```

* `package` 指的是定義套件名稱。
* 每個 `.go` 原始碼開頭都必須要宣告 `package` 。
* `main` 套件是有特殊意義的套件名，它是程式的起始點。執行程式的時候，將會從 `main` 套件開始。

* 若把 `main` 名字換成其他名字，再執行一次，將會出現錯誤訊息：
```
$ go run main.go
go run: cannot run non-main package
```

* 不能跑非 `main` 的套件。這個概念與大多數 PHP 框架的 `index.php` 類似，是所有 `request` 的起始點。

**import**
```
import "fmt"
```

* `import` 表示要引用套件，而 `fmt` 套件是 `Go` 內建的處理格式化輸入輸出函式庫。
* `Hello World` 的目的是要輸出文字，所以需要這個函式庫。

**func**

* 這是定義函式，也就是要寫流程了。
```
func main() {
	fmt.Println("Hello, 世界")
}
```

* `func` 定義了程式流程，供其他函式呼叫使用。
* 程式碼可以看到兩個函式，一個是現正定義的 `main` ，另一個則是 `fmt` 套件所提供的 `Println`函式，把後面帶入的文字印出來，然後再另外加一個換行。

* Go 語言有套件庫的概念，同時的函式也有能見度的規範。 
* Go 採用比較特別的方法：開頭**大寫**的函式是 `public` ，不同的套件庫可以呼叫 `public func` ；開頭**小寫**的則是 `private` ，只限套件庫內部使用。
* `Println` 是屬於 `fmt` 套件的 `public func` ，因此雖然套件庫不同（main 與 fmt），仍然可以正常呼叫。
* `func main` 比較特別，它會搭配 `package main` 一起使用。
* `package main` 是所有程式的進入點
* `go run` 會把 `package main` 的 `func main` 拿出來呼叫。

* `go run main.go` 實際上就是執行 `fmt.Println("Hello, 世界")`