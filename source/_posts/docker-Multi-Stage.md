---
title: docker-Multi-Stage
date: 2019-12-22 15:41:19
tags: 
  - docker
  - dockerfile
  - single build
  - multiple build
  - multi-stage
  - scratch
categories: 
  - docker
---

## Docker Multi-Stage compile Golang Image

<!--more-->
---

### Single Build
1. Go 語言
```golang=
package main

import "fmt"

func main() {
  fmt.Println("Hey Yu!")
}
```

2. 用 `alpine` 的 Golang Image 來編譯出執行檔
```
FROM golang:alpine
WORKDIR /app
ADD . /app
RUN cd /app && go build -o app
ENTRYPOINT ./app
```

>補充說明：
>1. `WORKDIR <工作目錄路徑>`
 使用`WORKDIR`指令用來指定工作目錄（或稱為當前目錄），以後各層的當前目錄就被改為指定的目錄，如該目錄不存在，`WORKDIR`會幫你建立目錄。
>2. `COPY`和`ADD`兩個的功用都一樣，就是將檔案複製進去image
 差別在於：
  • `COPY`只能複製本機端的檔案或目錄
  • `ADD`能增加遠端url的檔案到docker image
  • `ADD`能順手將本機端複製進去的tar檔解開(遠端的tar不行！)
 在實例上並不建議使用`ADD`來抓取網路上的檔案，會使用`RUN curl or wget`的方式。
 原因是使用一次`ADD`指令會增加docker image layers一次，layers越多，docker image size就會越大
>3. `ENTRYPOINT`的部分，待其他章節說明


3. 執行編譯指令
```
docker build -t yu/go-app .
docker run --rm yu/go-app
```
檢查看看編譯出來的 Image 大小，使用 `docker images | grep go-app`，會發現 Image 大小為 258 MB

---
### Multiple build
Multiple build 可以在 `Dockerfile` 使用多個不同的 Image 來源
```
# build stage
FROM golang:alpine AS build-env
ADD . /src
RUN cd /src && go build -o app
 
# final stage
FROM alpine
WORKDIR /app
COPY --from=build-env /src/app /app/
ENTRYPOINT ./app
```
`AS` 及 `--from` 互相溝通，以往需要寫兩個 `Dockerfile`，現在只要一個就可以。最後執行編譯指令:

```
docker build -t yu/go-app .
docker run --rm yu/go-app
```
最後大小為 6.35 MB，比較小了

參考範例:
`cmd/server/server.go` 需要被更改成你的檔案
```
FROM golang:1.13 as builder

WORKDIR /app
COPY . /app
RUN CGO_ENABLED=0 GOOS=linux GOPROXY=https://proxy.golang.org go build -o app cmd/server/server.go

FROM alpine:latest
# mailcap adds mime detection and ca-certificates help with TLS (basic stuff)
RUN apk --no-cache add ca-certificates mailcap && addgroup -S app && adduser -S app -G app
USER app
WORKDIR /app
COPY --from=builder /app/app .
ENTRYPOINT ["./app"]
```

---

### Docker 提供的 image scratch
並不適用於每一個案例
```
FROM golang as base

WORKDIR /app

ENV GO111MODULE=on \
    CGO_ENABLED=0 \
    GOOS=linux \
    GOARCH=amd64

COPY go.mod .
COPY go.sum .

RUN go mod download

COPY . .

# it will take the flags from the environment
RUN go build

### Certs
FROM alpine:latest as certs
RUN apk --update add ca-certificates

### App
FROM scratch as app
COPY --from=certs /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=base app /
ENTRYPOINT ["/app"]
```

### REFERENCES
* [用 Docker Multi-Stage 編譯 Golang 最小 Image](https://blog.wu-boy.com/2017/04/build-minimal-docker-container-using-multi-stage-for-go-app/)
* [Slim Docker images for your Go application](https://dev.to/andrioid/slim-docker-images-for-your-go-application-11oo)
* [使用 Docker build 一個 Golang image](https://ithelp.ithome.com.tw/articles/10209304)