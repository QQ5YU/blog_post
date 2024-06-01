---
title: docker 初學筆記 (image、container、system 指令)
date: "2023-12-16"
tags: docker
---

# docker 初學筆記
## image 基本指令
### docker images
> 查看所有本機中的 image 檔

**options**
* **-a or -\-all**: 列出完整的映像檔層次資訊
* **-q or -\-quiet**: 只列出映像檔ID
* **-\-format goLen樣板語法**: 指定列出格式，ex. docker image ls --format "{{.ID}}: {{.Repository}}"
* **-\-digests**: 顯示 image digest (摘要)

### docker pull Registry 的位址/imageName:tag
> 從Docker Hub下載映像檔(沒有tag的話預設會拉最新版)，Registry 的位址預設為官方 registry

### docker push userName/repoName:tagName
> 將 image 推送到 Docker Hub

### docker tag repoName:tagName
> 替本機映像檔加標籤名稱

**一個映像檔可以有很多不同的tag name**
> 將 latest 和 u14 兩個 TAG 指到同一個映像檔ID
```javascript!
docker tag joshhu/webdemo:latest joshhu/webdemo:u14
```
### docker build 
> 從現成的映像檔為基礎，自行建立全新的映像檔

**options**
* **-t or -\-tag**: 幫 image 加上 tag
* **-\-no-cache=true | false**: 建立 image 時不使用 cache
* **-f or -\-file fileName**: 用於自定義 DockerFile 檔案名稱時，讓 docker 知道哪個檔案是 DockerFiles
* **-\-target 階段名稱**: 多階段 DockerFile 中，可指定要 build 的階段

**Example**
> 常用範例: **docker build -t imageName:tag**

```dockerfile!
docker build -t vieux/apache:2.0 .
```

### docker image hisotry name:tag
> 查看映像檔是由哪些映像層 ( IMAGE ) 由下往上堆疊而成及每層映像層的資訊

**options**
* **-\-no-trunc**: 看完整的內容
* **-\-format "{{.title}}"**: 看特定欄位，後面接的參數是 Go Template 的格式

### docker rmi
> 刪除本機中存放的映像檔(只能刪除沒被使用的容器)

**options**
* **-f**: 強制刪除 (對正在使用的容器有效)
* **-\-no-prune=true**: 只刪除有 tagname 的 image，留下許多映像檔共用的母層次

### docker rmi -f $(docker images -aq)
> 配合 Linux 的批次指令來一次清乾淨所有的映像檔

### docker save -o file.tar imageName
> 想要和其它人交換時 ，不想上傳到Docker Hub，不想自己架設私有Docker Registry，就可以用這兩個參數存成tarball格式及匯出。

**options**
* **-o: (output)** 指定保存 Docker 映像的 tar 文件的輸出路徑和名稱


### docker load -\-input file.tar 
> 將tarball還原回映像檔格式

**options**
* **-\-input**: 輸入檔案的意思

:::info
save 及 load 的另一種寫法，save 的寫法 **docker save imageId > fileName **，
load 的寫法 **docker load < fileName**
:::

--- 

## container 的基本指令

### docker create imageName
> 建立 container 並執行指令

### docker run imageName:tag
> 建立 container 並執行指令

:::warning
docker run 指令每次都會 new container，並不會重複利用 container
:::

**options**
* **-c**: 使用主機 CPU 的多少資源(主機 = 1024 CPU share)
* **-\-cpus**: 限制容器可以使用的 CPU 核心數量
* **-d or -\-detach**: 背景(後台)執行，回傳 containerID，想看輸出結果可透過 docker logs
* **-\-name containerName**: 指定容器名稱
* **-p or -\-publish localPort:containerPort**: 開 port 號，把 localPort 的流量轉到 containerPort
* **-P or -\-publish-all**: 將容器內所有的 port 映射到主機的隨機 port 上
* **-\-rm**: 執行結束後自行刪除 container
* **-w or -\-workdir**: 設定 container 內的工作目錄，在這個路徑下執行指令
* **-i or -\-interactive**: 讓 container 的標準輸入保持打開，相當於 Linux 上的 STDIN
* **-t or -\-tty**: 告訴 Docker 要分配一個虛擬終端機（pseudo-tty）並綁定到 container 的標準輸入上，相當於 Linux 上的 STDOUT
* **-it**: 啟動互動模式，進入 Linux 主機，可直接對 container 下指令
* **-di**: 將 container 放入背景，並且讓他保持基本==輸入==的能力
* **-dt**:將 container 放入背景，並且讓他保持基本==輸出==的能力
* **-idt**:將 container 放入背景，並且讓他保持基本輸入或 輸出的能力
* **privileged**: 擁有容器特權，有點類似 root 特權，可進行一些修改操作
* **-\-network networkName**: 與 network 連接
* **-e envName=value or -\-env envName=value**: 設置環境變數
* **-v localFolderPath:containerFolderPath**: **Bind Mount**，將本機資料夾與容器資料夾綁定，兩邊連動同步。
* **-\-mount ==type=bind==,source="本機資料夾目錄",target="容器內資料儲存的目錄"**: **Bind Mount**，等同於上方 -v 指令
* **-v volumeName:containerFolderPath**: 掛載 volume 空間到 container 上
* **-\-mount ==type=volume==,source="本機資料夾目錄",target="容器內資料儲存的目錄"**: **掛載 volume 空間，同上 -v 指令**
* **-\-volumes-from containerID**:*掛載指定 container 上掛載的所有 volume 空間**
* **-\-mount ==type=tmpfs==,destination=containerPat**: 掛載一個臨時文件系統(tmpfs)到容器中，當容器停止時，就會移除資料，常用來儲存暫存性或敏感資料。
* **-m**: 限制容器使用多少儲存空間(超出限制一倍 docker daemon 會強制關閉容器)
* **-\-memory-swap**: 指定 Container 儲存空間容量

:::warning
**--volume** 是**主機與容器之間**的資料共享，**--volume-from** 是**容器之間**的資料共享
:::

* **-\-link name:alias**: 連結兩個 container，name 為要連結的容器名稱，alias 為這兩個容器連接後的別名
* **-\-dns=IP_ADDRESS**: 自定 DNS 伺服器
* **-\-dns-search=DOMAIN**: 設定容器的搜尋域
* **-h or -\-hostname=HOSTNAME**: 設定容器的主機名
* **-\-add-host hostName:IP 位置 **: 對服務添加額外的 host 名稱及 IP 位置映射，對於容器內須訪問其他 host 時非常有用
* **-\-net=options**:使用主機的網路命名空間，使得容器與主機共享網路空間
> 預設為 bridge，表示使用 Docker 的橋接網路


### docker kill
>  刪除執行中的容器，但容器本體還是存在

### docker rm containerID
> 刪除停止情況的 container

**options**
* **-f or -\-force**: 強制刪除 (對正在使用的容器有效)
* **-\-no-prune=trun**: 只刪除有 tagname 的 image，留下許多映像檔共用的母層次

### docker pause containerID
> 暫停執行中的 container，**仍佔有記憶體，服務不中斷**

### docker unpause containerID
> 恢復暫停中的 container

### docker stop containerID
> 停止執行中的 container，但**不佔有記憶體，服務中斷**

**options**
* **-t or s-\-time=num**: 等待幾秒
* **-s or -\-signal signal**: 發送甚麼訊號給容器

### docker start containerID
> 啟動停止中或是剛 create 的 container

**options**
* **-a or -\-attach**: 列出容器中的 output

### docker restart containerID
> 重新啟動 container

### docker wait 
> 讓 container 暫停直到 container  s停止為止

### docker rename
> 幫 container 改名

### docker attach containerName
> 進入容器操作，要先啟動容器，需要注意的是如果 exit 容器，容器將會終止運作

**options**
* **-d or -id**:離開 container 時該container 停止
* **-td**: 離開 container 時該container 在背景執行

### docker exec containerName command
> 在外部向 container 內執行指令，讓 container 除了 run 本來的 image 內容也可以同時做其他指令。

**options**
* **-i or -\-interactive**: 保持 STDIN 開啟
* **-t or -\-tty**: 分配 pseudo-tty，相當於 STDOUT
* **-u or -\-user**: 指定使用者名稱或是 UID，指定的使用者必須事先存在。

**常用 Example**
> 進入 container 內，但是 container 必須是啟動狀態
```javascript!
docker exec -it containerName bash
```
:::warning
**docker attach vs docker exec -it**: 使用 attach 進入容器會在離開容器時終止容器，但使用 exec -it 進入容器後離開並不會終止容器。
:::

### docker export conatinerName
> 把 container 的檔案系統使用 tar 打包輸出

### docker import fileName imageName
> 把打包的 tar 檔的檔案系統導入到 Docker repository 裡

:::info
**docker load vs docker import**: load 會將完整的儲存檔案紀錄都載下來 (檔案較大)，但 import 只會保存容器當時的記錄及狀態，不會留存歷史紀錄或是 tag、作者之類的資訊
:::

### denter containerName 
> 進入 run 在背景的 container

### docker commit containerName  imageName
> 將指定的容器的儲存層保存，建立成映像檔
* **options**
    * **-m or -\-message**: 提交的說明資訊
    * **-a or -\-author**: 更新的使用者資訊

:::warning
使用 docker commit 產生的 image 被稱做黑箱映像，意旨只有製作此映像的人才知道此映像操作過的內容，使得維護困難，通常不建議使用。
:::

### docker secret create secretName
> 創建一個或多個密鑰，只能用於 swarm mode

**Example**
* **-d or -\-driver** : 密鑰的驅動程式
* **-\-label**: 密鑰標籤
* **-\-template-driver**: 樣板驅動程式
```dockerfile!
# 從檔案中創建
docker secret create my_secret_key /path/to/your/secret/file

# 隨機數據創建密鑰
docker secret create my_secret_key -
```

### docker secret rm secretName
> 刪除 secret 

### docker secret ls
> 列出所有密鑰

### docker secret inspect secretName 
> 查看 secret 詳細資訊

**Example**
* **-f or -\-format** : 指定輸出格式 ex.json
* **-\-pretty**: 優化輸出排列方式


---
## container 的狀態查看
### docker logs containerID
> 將 container 內的輸出顯示到螢幕上，也可以查看日誌檔案內容

### docker conatiner top containerId
> 查看容器內的進程（process）資訊，列出在特定容器中運行的所有進程及其相關資訊

### docker inspect containerName
> 檢查 container 的詳細資訊

### docker stats
> 查看 container 的CPU、記憶體及網路使用

### docker port
> 查看 container 的 port 的使用

## docker ps
> 查看所有**正在執行的** container

**options**
* **-a or -\-all**: 查看所有 container 連執行完但還沒消失的都會列出

### docker top containerId
> 查看 container 在主系統中的記憶體使用

### docker dip
> 查看 container 的IP

### docker dpid
> 查看 container 的 pid (process ID)，指容器中正在運行的進程的唯一識別符號

---
## container 和主系統之間的操作
### docker cp 
> 容器與本機間的檔案複製
* **複製本地檔案到容器**
    * docker cp local_path containerName:containerPath
* **從容器複製檔案到本地端**
    * docker cp containerName:containerPath local_path

:::danger
與 DockerFile 中的 COPY 指令類似，不同之處在於 COPY 只能把本機的檔案複製到容器內，但 cp 指令可以雙向複製。
:::

### docker diff
> 查看 container 檔案系統，與 image 檔案系統的差異，方便分辨容器啟動後對檔案做了哪些修改

### docker events
> 列出某個時間點之前或之後的事件

---

## docker network 指令
> 與 docker run --link 的功能相似，但官方建議使用 docker network

### docker network create netWorkName
> 建立網路，主要是連接容器之間的通訊

**options**:
* **-d or --driver options**: 選擇驅動，詳細參數參考官網 [Docker network -d](https://docs.docker.com/network/#drivers)
* **-\-geteway**: 設定 Network geteway
* **-\-subnet**: 設定 Network subnet

### docker network connect networkName containerName
> 連接網路

### docker network ls
> 查看當前網路設定配置

### docker network inspect networkName
> 查看 network 狀態

### docker network rm networkName
> 刪除網路

---
## docker volume 指令
> 從 docker 內切出一塊獨立空間出來，提供容器使用，讓容器可以儲放資料。

:::info
**volume vs bind**: volume 是由 docker 管理的，使用時會在主機上建立 docker 儲存資料夾，而 bind mount 完全是依賴主機的 filesysteam，表示這個資料夾原本要存在於主機上才讀取的到
:::

### docker volume create -\-name volueName
> 創造 volume

### docker volume ls 
> 列出所有 volume

### docker volume inspect volumeName
> 查看 volume 的詳細資料，Mountpoint 代表著 Volume 的路徑

### docker volume rm volumeName
> 刪除指定 volume

### docker volume prune
> 刪除所有未被 container 使用到的 volume

### Remote Volume
> 利用遠端儲存服務與容器連結，允許容器與遠端儲存服務進行數據交換

**語法**
* **sshcmd**: 共享的機器username@IP(Host3):Host3 路徑
* **password**: 共享機器密碼
* **sshvolume**: 為volume命名
* **create —\-driver driverName**: 創建並指定 driver

```dockerfile!
docker volume create --driver 'Driver Name'\
-o sshcmd='username@IP':'Host Path'\
-o password='password'\
'Volume name'
```

---

## docker manifest (experimental)
> manifest 是一種描述 docker image 中多平台支持的數據格式

### dokcer manifest inspect imageName:tag
> 查看特定 image 的 manifest 清單，可用來檢視此 image 支持的不同平台（例如不同的 CPU 架構）以及其他相關的詳細訊息

### dokcer manifest create manifestListName manifest
> 建立一個 manifest list

**manifestListName**
* 格式通常為 username or groupName/manifestName
* Ex. username/test

**manifest**
* 將要添加的 manifest 列表加入

**options**
* **-a or -\-amend**: 修改 manifest 列表

### dokcer manifest annotate manifestListName manifest string
> 幫 manifest list 添加註解

**options**
* **-\-os**: (operating system) 設定運作的系統環境
* **-\-arch**: (architecture)設定系統架構


[Docker manifest tutorial](https://yeasy.gitbook.io/docker_practice/image/manifest)

---
## docker buildx 
> docker 18.09 後的版本才能用，透過使用  buildkit 擴展 image build 的能力

**在 docker 中啟用 buildkit**
> 在 docker 資料夾下的 `daemon.json`檔案中加入以下程式

```json!
{
  "features": {
    "buildkit": true
  }
}
```

### docker buildx version
> 查看版本

### docker buildx inspect --bootstrap
> 查看有哪些架構可以使用

### docker buildx create 
> 建立一個 builder

**options**
* **-\-use**: 建立後立即使用
* **-\-name builderName**: 命名
* **-\-driver docker-container| kubernetes | remote**: 指定驅動程式
* **driver-opt**: 指定驅動程式提供額外的選項或配置
* **-\-append builderName**: 將現有的 builder 添加到這個 builder 中
* **-\-node nodeName**: 指定要建立或修改的 node
* **-\-leave**: 移除 node 而不是更改 node
* **-\-platform platform**: 指定 node 支援的平台

### docker buildx use builderName
> 使用 builder


### docker buildx build dockerFilePath
**options**
* **-\-platform**: 設定 build 環境

### docker buildx ls
> 列出有哪些 builder


---
    
## docker system

### docker system prune
> 移除所有沒有使用的 containers, networks, images (包含 dangling), volumes


### docker system df
> 檢視images、containers、volumes 所占用的空間

---
## docker daemon
> docker 守護程式，可透過 docker 資料夾中的 `daemon.json` 檔案修改配置或是直接使用 `docker 指令`

### network 配置
> 這裡講解的是 `docker 指令` 方式，也可以透過 json 格式修改 Ex. `{ "icc": false }`

**options**
* **-b bridge or -\-bridge=bridge**: 指定容器掛載的bridge
* **-\-bip=CIDO**: 制定 docker0 的掩碼
* **-H socket or -\-host=socket**: docker server 接收命令的通道
* **-\-icc=true|false**: 是否支援容器之間通訊
* **-\-ip-forward=true|false**: 啟動或禁止 IP 轉發功能，讓容器可以存取外部網路
* **-\-iptables=true|false*: 是否允許 docker 新增 iptables 規則，iptables 為 Linux 上的防火牆軟體
* **-\-mtu=bytes**: 設置 docker 容器的網路最大傳輸單元大小



---
## docker 專有名詞及概念
### 虛懸映像 (dangling image)
> 虛懸映像 (dangling image)，由``docker build`` 或 ``docker pull``產生，指的是 repositoryName、tag 都顯示為 `<none>` 的image，常見於官方發布新版本後，原先的映像檔使用新版本的映像，而導致舊版本沒被使用，這樣會占空間，透過``docker images`` 會顯示。

**只查看 dangling images
* **-f or -\-filter dangling**: 只顯示虛懸映像
* **-f or -\-filter since=imageName:tag**: 只顯示從特定 image **後**建立的 image
* * **-f or -\-filter 换成 before=imageName:tag**: 只顯示從特定 image **前**建立的 image
* * * **-f or -\-filter 换成 label=label**: 只顯示從特定 label 的 image

```dockerfile!
docker images -f dangling=true
```


### 中間映像 (intermediate image)
> 中間映象是指建立 image 所需依賴的父層 image(由於鏡像分層產生)，透過``docker images -a`` 才會顯示，不會占空間，也不應該刪除，否則會導致上層映像依賴遺失出錯。

:::warning
**中間映像 vs 虛懸映像**，可透過指令區分，透過 ``docker images -a`` 指令出現的空映像為中間映像，而透過``docker images`` 顯示的映像為虛懸映像。
:::

### 容器間溝通要素
* 網路拓樸互聯，預設是連到 bridge docker0 
* iptables 防火牆軟體允許通過


---

## 指令捷徑 - alias
**alias 指令名稱 = "指令"**

**Example**
```dockerfile!
# 使用 npm
alias npm="docker run -it --rm -v \$PWD:/source -w /source node:14-alpine npm"
```
---

## 將 image 推到 repository

**Step1: docker login**
> 登入 docker hub

```dockerfile!
docker login
```

**Step2: docker tag**
> 給 image 加上 tag (不一定要做)，目的是方便識別版本及管理、易於共享、容易追朔。

```dockerfile!
docker tag your-image-name your-dockerhub-username/your-image-name:tag
```

**Step3: docker push**
> 推到 repository 上

```dockerfile!
docker push your-dockerhub-username/your-image-name:tag
```

**驗證方法: docker pull**

```dockerfile!
docker pull your-dockerhub-username/your-image-name:tag
```

---

## 建立私人 registry

[docker private registry](https://yeasy.gitbook.io/docker_practice/repository/registry)

