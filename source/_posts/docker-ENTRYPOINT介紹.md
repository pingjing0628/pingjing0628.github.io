---
title: docker-ENTRYPOINT介紹
date: 2019-12-22 22:08:51
tags: 
  - docker
  - dockerfile
  - Entrypoint
categories: 
  - docker
---

## Docker ENTRYPOINT 

<!--more-->
---

* `ENTRYPOINT`的目的和 `CMD`一樣
* 當指定了`ENTRYPOINT`後，`CMD`的含義就發生了改變，不再是直接的運行其命令，而是將`CMD`的內容作為參數傳給`ENTRYPOINT`指令，換句話說實際執行時，將變為：
`<ENTRYPOINT> "<CMD>"`

---
### 狀況一
假設需要得知自己當前公網IP，可以先用CMD來實現：
```
FROM ubuntu:18.04
RUN apt-get update \
    && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
CMD [ "curl", "-s", "https://ip.cn" ]
```
執行 `docker build -t myip`建立image
則查詢ip執行
```
docker run myip
當前 IP：61.148.226.66 來自：北京市 聯通
```

上述的`CMD`中可以看到實質命令是`curl`，那若希望顯示HTTP header 訊息，就需要加上`-i`參數。
```
docker run myip -i
docker: Error response from daemon: invalid header field value "oci runtime error: container_linux.go:247: starting container process caused \"exec: \\\"-i\\\": executable file not found in $PATH\"\n".
```
出現找不到可執行檔案的錯誤訊息，因為這裡的`-i`替換了原來的`CMD`，而不是添加在原來的`curl -s https://ip.cn`後面。而`-i`根本不是命令，所以自然找不到，必須重新完整的輸入命令才可以執行
`docker run myip curl -s https://ip.cn -i`

**使用Entrypoint**
```
FROM ubuntu:18.04
RUN apt-get update \
    && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*
ENTRYPOINT [ "curl", "-s", "https://ip.cn" ]
```

```
 docker run myip
當前 IP：61.148.226.66 來自：北京市 聯通

$ docker run myip -i
HTTP/1.1 200 OK
Server: nginx/1.8.0
Date: Tue, 22 Nov 2016 05:12:40 GMT
Content-Type: text/html; charset=UTF-8
Vary: Accept-Encoding
X-Powered-By: PHP/5.6.24-1~dotdeb+7.1
X-Cache: MISS from cache-2
X-Cache-Lookup: MISS from cache-2:80
X-Cache: MISS from proxy-2_6
Transfer-Encoding: chunked
Via: 1.1 cache-2:80, 1.1 proxy-2_6:8006
Connection: keep-alive

當前 IP：61.148.226.66 來自：北京市 聯通
```
因為當存在`ENTRYPOINT`後，`CMD`的內容將會作為參數傳給`ENTRYPOINT`，而這裡`-i`就是新的`CMD`，因此會作為參數傳給`curl`，從而達到了我們預期的效果。

---
### 狀況二
啟動容器就是啟動主進程，但有些時候，啟動主進程前，需要一些準備工作。
比如`mysql`的資料庫，可能需要一些資料庫配置、初始化的工作，這些工作要在最終的`mysql`服務器運行之前解決。
此外，可能希望避免使用`root`使用者去啟動服務，從而提高安全性，而在啟動服務前還需要以`root`身份執行一些必要的準備工作，最後切換到服務使用者身份啟動服務。或者除了服務外，其它命令依舊可以使用`root`身份執行，方便調整等。
這些準備工作是和容器`CMD`無關，都需要事先進行一個預處理的工作。這種情況下，可以寫一個腳本，然後放入`ENTRYPOINT`中去執行，而這個腳本會將接到的參數（也就是<CMD>）作為命令，在腳本最後執行。比如官方鏡像redis中就是這麼做的：
```
FROM alpine:3.4
...
RUN addgroup -S redis && adduser -S -G redis redis
...
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 6379
CMD [ "redis-server" ]
```
在最後指定了`ENTRYPOINT`為`docker-entrypoint.sh`腳本。

```
#!/bin/sh
...
# allow the container to be started with `--user`
if [ "$1" = 'redis-server' -a "$(id -u)" = '0' ]; then
    chown -R redis .
    exec su-exec redis "$0" "$@"
fi

exec "$@"
```
該腳本的內容就是根據`CMD`的內容來判斷，如果是`redis-server`的話，則切換到redis使用者身份啟動服務器，否則依舊使用root身份執行。
```
docker run -it redis id
uid=0(root) gid=0(root) groups=0(root)
```

---

### REFERENCES
* [Entrypoint](https://yeasy.gitbooks.io/docker_practice/image/dockerfile/entrypoint.html)