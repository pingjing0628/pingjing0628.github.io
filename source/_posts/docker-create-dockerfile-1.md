---
title: docker-create dockerfile(1)
date: 2019-12-22 14:39:46
tags: 
  - docker
  - dockerfile
  - nginx
categories: 
  - docker
---

## Create Dockerfile

<!--more-->
---

### 產出簡單的dockerfile
1. 先產出簡單的 `dockerfile`，再添加相關指令
`FROM alpine:3.9`
2. 將 `dockerfile`轉換成 `image` 再用此 `image`生成 `container`
```
docker build -t dockerfile .
docker run -it -p 8080:8080 dockerfile
```
---

### 嘗試透過一般流程指令安裝nginx
```
apk update upgrade
apk add nginx
nginx -t
```

**問題一**
`nginx: [emerg] open() "/run/nginx/nginx.pid" failed (2: No such file or directory)`
原因是找不到nginx.pid，經檢查是沒有/run/nginx 這個資料夾，所以nginx無法執行成功
```
mkdir -p /run/nginx
nginx -t
```

**問題二**
測試 http://localhost:8080/，依舊不行
原因是設port 8080:8080 ，但nginx裡設定為80，所以添加一個設定檔
```
cat << EOF > /etc/nginx/conf.d/localhost.conf
server {
        listen       8080;
        server_name  localhost;
location / {
            root   /var/www;
            index  index.html index.htm;
        }
}
EOF
```
如此一來會多了一個檔案 `/etc/nginx/conf.d/localhost.conf`
且指向 `/var/www`，現在給一個index.html
`echo hi >> /var/www/index.html`

執行 `nginx -s reload`

**問題三**
`nginx: [error] invalid PID number "" in "/run/nginx/nginx.pid"`
原因是只有創建資料夾，還尚未啟用nginx ，來啟用nginx
`nginx -c /etc/nginx/nginx.conf`


###安裝nginx成功後需將指令寫進dockerfile
```
FROM alpine:3.9
RUN apk update
RUN apk upgrade
RUN apk add nginx
RUN mkdir -p /run/nginx

COPY localhost.conf /etc/nginx/conf.d/localhost.conf

COPY index.html /var/www/index.html

CMD nginx -g "daemon off;"
```

一次 `command`，在 `dockerfile`就是 `RUN`
中間新增兩個檔案，是先把檔案創建好，再COPY進去
最後給一個要執行的指令 ，使用 `nginx -g "daemon off;"`，是為了能正確在容器上正常執行，需要保持 `daemon off;`

###測試是否能執行
因為要執行在「背景」，跟剛剛run的指令不一樣
```
docker build -t dockerfile .
docker run -d --name nginx -p 8080:8080 dockerfile
```
接著可以再docker ps 中檢查

---
### REFERENCES
* [隨手寫寫‒‒Docker(5)Dockerfile](https://medium.com/@tasslin/%E9%9A%A8%E6%89%8B%E5%AF%AB%E5%AF%AB-docker-5-dockerfile-3c26d5043279)