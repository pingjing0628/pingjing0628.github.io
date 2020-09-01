---
title: What is Kubernetes?
date: 2019-11-11 15:44:19
tags:
	- Kubernetes
	- 
categories:
	- Kubernetes
	- deploy
---
### Kubernetes

<!--more-->
***

* 容器的全自動管理/調度平台，誕生自Google Borg
* 不限制application類型，只要能容器化都吃
* 具有各種抽象化的資源類型
	* web/database
	* task runner
	* auto scaler
	* Ingress

### Why is Container

* 一致性的執行環境
* 方便的應用部署，有利於CI/CD
* 清楚的分離Dev與Ops，有利於分工
* 以應用為中心的管理方式

### Docker
---
#### Docker生命週期
1. Code your app 
2. Write Dockerfile
3. Create images. Defined at Dockerfiles
4. Define services at writing
5. Run containers / Compose app
6. Test your app or microservices
7. Push or continue developing
* 寫好的dockerfile，build 成image後只是存在你build 好的那台機器上面，所以若是要share給別人使用的話，就是要放到第三方的地方，所以image會放在registry的地方
{% img https://docs.microsoft.com/zh-tw/dotnet/architecture/microservices/docker-application-development-process/media/image1.png 1000 400 vi-vim-cheat-sheet %}

#### Docker Registry
* 維護方式跟 github 機制有87%像，但只是拿來放docker image
* DockerHub ，目前最大的public registry
* private registry
	* VMware Harbor
	* gitlab registry
		* 在900之後，有一個內建的registry，gitlab.com上面就有提供一個免費的registry，可以build 自己的code，丟到gitlab registry
	* Cloud: AWS/GCP/Azure...

### Kubernetes 資源介紹
---
#### Kubernetes Nodes
* Node (就是實體機，分成以下兩個)
	1. Worker Node
		* 負責 Resource 的執行
		* 監控 Resource 的健康狀態
	2. Master Node
		* 管理與監控 Node
		* 負責調度所有的 Resource
		* 儲存整個 Cluster 的運作狀態
* 架設方式
	* 自架：minikube/kubeadm/Rancher 2.x(不建議自己架，因為會有很多雷)
	* 直接用 Cloud AWS/GCP/Azure/DigitalOcean

#### Kubernetes 抽象資源
1. Namespace
* 一組資源的集合
* 隔離不同 namespace 之間的資源 (persistant volume除外)
* 預設有 kube-system/kube-public/default
{% img http://www.mundodocker.com.br/wp-content/uploads/2017/06/namespaces.png 600 300 vi-vim-cheat-sheet %}

2. Pod
* Kubernetes中最小執行單位
* 一個完整的 application 部署封裝，可能有一個以上的container
* 共享儲存空間/網路
{% img http://i0.wp.com/foxutech.com/wp-content/uploads/2018/02/Kubernetes-Pod.jpg 800 300 vi-vim-cheat-sheet %}

3. ReplicaSet
* 保證Pod數量跟設定中要求的一樣
ex: 譬如今天保證要有3台在運作，那麼就可以把replica設成3，假設今天把一台pod刪掉，那麼他今天就會再幫你生一台新的出來，那如果有多的話，就會把最舊的那台砍掉
* 少了就補齊，多了就砍掉，絕對不多不少
* 支援動態縮放
{% img https://prod-edxapp.edx-cdn.org/assets/courseware/v1/bfc5b38f02fcd9cd9c290750ecbc70a1/asset-v1:LinuxFoundationX+LFS158x+2T2017+type@asset+block/replicaset-1.png 500 300 vi-vim-cheat-sheet %}

4. Deployment 
* 管理 Pod 與 ReplicaSet
* 聲明式更新
* 支援Rolling Update

* **Rolling Update**
* 此image版本為1.0，若此時想換成2.0時
{% img https://codelabs.developers.google.com/codelabs/k8s-kickstart/img/6a62a850f2be0c9b.png 600 400 vi-vim-cheat-sheet %}
* 此時deployment會去開一個新的replicaSet，這個replicaSet會去開一個新的Pod，而這個Pod會去用2.0的image，此時2.0開了新的一台，而就的那邊就會關掉一台，且不會斷掉，以此類推，最後就會把舊的1.0的都關掉，變成2.0的Pod(先開新的，確定能夠使用，才把1.0關掉)
{% img https://codelabs.developers.google.com/codelabs/k8s-kickstart/img/8d107e36763fd5c1.png 800 400 vi-vim-cheat-sheet %}

5. Job (與deployment不同的是，deployment是提供一個持續性的服務，不會斷掉)
* 啟動Pod並且執行指定次數 (譬如像是migration，就是在上版的那一次要跑而已，run完即結束)
* Pod 如果執行錯誤，會重新開啟直到完成(重新開啟的次數是可以設定的)

6. Services
* 用來存取Pod的資源(因為Pod 不會讓外面的Cluster直接存取到，所以中間會經過Services)
* 可透過 Kubernetes內部的 DNS來對應
* 有提供各種的 Service Types：
	* ClusterIP
	* Node Port
	* LoadBalancer 