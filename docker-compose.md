---
title: docker 初學筆記 (dockerFile、docker compose、docker Hub)
date: "2024-01-18"
tags: docker, docker compose, dockerFile, docker hub
---

# docker 初學筆記
## dockerFile
> 優化原則: **不常變動的早點做，常變動的晚點做** (為了讓 docker 可以多使用 cache)

* **FROM imagesName:tag**
    * 指定 base image
    * Ex.FROM node:19-alpine (node環境)
* **MAINTAINER**
    * 維護者的資訊
    * 已棄用，建議使用 LABEL
* **AS**
    * 定義**階段**的指令，階段可隨意命名，通常搭配 FROM 一起使用
    * ex. **FROM **node:20-alpine **AS** build 定義為 build 階段
* **COPY fileName conatainerPath**
    * copy local file into docker container
    * Ex.COPY package.json /app/ 將 package.json 複製到 container 中 app 資料夾下
    **options**
    * **-\-from=階段名稱**: 取得先前階段所建置好的東西 (多階段的情況下)
    * **-\-chown=user**: 改變文件所屬的使用者及群駔

* **ADD sourcePath filePath**
    * 可以複製本地檔案到映像中，還可以解壓縮 tar 文件、URL　檔案下載與複製，並且可以自動解決 URL 路徑或是 tar 文件中的壓縮格式等
* **WORKDIR defaultPath**
    * 切換工作路徑，像是 terminal 中常用的 cd
    * Ex.WORKDIR /app
* **RUN command**
    * 在 docker build 時運行
    * 執行 command or 安裝套件
    * Ex.npm install express
    * 有時候會看到 RUN command && command ，表示第一個 command 執行成功才會執行下一個，如果失敗就不執行後面的 command
* **RUN addgroup groupName**
    * 用於創建一個用於 groupName 用戶的用戶組
* **RUN adduser -S -G groupName userName**
    * -S 建立 system user，通常是一個不具有登錄 shell 的用戶，僅用於運行服務或應用程式
    * -G groupName 指將新建立的用戶加入到 groupName 用戶組中
* **RUN chown -R userName:groupName**
    * chown (change owner) 用來設定檔案擁有者和檔案關聯群組的指令
    * -R 表示遞迴操作，將指定目錄下的所有檔案和子目錄都進行相同的操作。
    * userName:groupName 表示將擁有者設置為 userName 用戶，群組設置為 groupName 用戶組


* **USER userName:groupName**
    * 改變容器的使用者身份，將其切換為 userName 用戶
    * 也可以單純指定使用者**USER userName**
    * 優點: 限制容器內部使用者使用權限
* **ENV name=value**
    * 在 container 內定義環境變數
    * 可在 run 時透過 -e name=value 改變
* **ARG name=value**
    * 定義環境變數，只能活在 build image 階段，在 docker build 時可更改變數，變數使用方式`` --build-arg argName=value``。
    * 放在 FROM 之前的 ARG 只有 FROM 能使用，想要在 FROM 之後使用必須在 FROM 之後宣告。
* **VOLUME ["path"] or path**
    * 在 container 中建立掛載點或宣告磁碟區，為了把資料寫入到實體機器上 (資料存在主機的文件裡)
    * 也可以用 docker run -v path
* **EXPOSE port**
    * 聲明 container 要使用的 port，並不會自動映射 port ( 與 `-p` 指令不同)
* **CMD ["executable", "\<param1>"]**
    * 在docker run 時運行
    * 只能有一條 CMD 指令
    * 可被 command line 中 docker run 語句複寫
    * Ex.CMD ["node", "server.js"] 相當於再 terminal 執行 node server.js

* **ENTRYPOINT [ "\<executeable>" , "\<param1>"]**
    * 類似 CMD，但不會被 command line 中 docker run 語句複寫 (使用 -\-entrypoint參數時例外)
    * 還可以執行 Dockerfile 外的 script 檔案 ex.ENTRYPOINT ["docker-entrypoint.sh"]


* **SHELL**
    * 用於指定指令的 shell
    * SHELL ["executable", "parameters"]
    * Linux 預設為 ["/bin/sh", "-c"]

* **HEALTHCHECK CMD linuxCmd**
    * 透過發送指令來確認容器狀態是否良好
    * ex.HEALTHCHECK CMD curl \-fail or -f http://localhost/health
    **options**
    * **-\-fall or -f**: 表示如果 HTTP 請求的響應狀態碼不是成功（即不是 2xx），則終止並返回一個非零的退出碼，這樣就可以被 HEALTHCHECK 用於判定容器的健康狀態。
    *  **-\-interval=second**: 設定檢查時間間隔 (預設為30s)
    *  **-\-timeout=second**: 檢查時間超過多久則視為失敗
    *  **-\-start-period=second**: 容器啟動後，健康檢查之前的等待時間 (預設為0s)
    *  **-\-start-interval=second**: 容器啟動後等多久後第一次進行健康檢查 (預設為5s)
    *  **-\-retries=num**: 若是健康檢查為不健康時，會再重試幾次 (預設為3次)
* **ONBUILD instruction**
    * 配置目前 image 若作為其他 image 的 base image 所執行的指令
* **STOPSIGNAL signal**
    * 設定停止容器所要傳送的系統呼叫訊號，使用的訊號必須是核心系統呼叫表中的合法的值
    * ex.STOPSIGNAL SIGKILL
* **SHELL ["executable", "parameters"]**
    * 指定其他指令使用shell 時的預設shell 類型
    * 預設值為["/bin/sh","-c"]
* **LABEL labelName=value**
    * 描述有關 image 的一些訊息，例如作者、版本、描述、維護者等
    * ex.LABEL version="1.0"

## exec 模式與 shell 模式
> exec 模式是建議的使用模式，因為當執行任務為 process ID 1時，我們可以透過 docker 的 stop 命令優雅的結束容器


### exec 模式
> 容器的指令會直接進入 PID1( 1號 process )，但無法在指令上使用環境變數

**Example**
```dockerfile!
CMD ["php", "artisan", "serve"]
```

**使用環境變數方法**
> 必須使用 shell 語法

```dockerfile!
FROM ubuntu
CMD ["/bin/sh", "-c", "echo $HOME"]
```

### shell 模式
> 以 shell 形式的命令啟動在shell 中執行的 process，相當於以 /bin/sh -c "task command" 方式執行，容器的指令會先將是/bin/sh -c 排入 PID1( 1號 process )，再將指定的命令排定為子process (PID7)，目的是讓我們執行的指令可以取到環境變數

**Example**
```dockerfile!
FROM node_project
CMD npm run $NODE_ENV
```


### dockerFile 撰寫技巧

#### 多行指令
>  用 \\ 表示換行

**Example**
```dockerfile!
RUN apk add --no-cache \
        git \
        unzip
```

---

## buildx in DockerFile
> 還處於實驗狀態，因此使用的 DockerFile 開必須加上 `# syntax = docker/dockerfile:experimental`

### RUN --mount=type=<type>
**type options**
* **bind**: (預設值) 將一個 image 或是 build 階段掛載到指定位置，Read only。
    **options**
    * **target**: 掛載到容器中的目標路徑
    * **from**: 指定來源 image
    * **source**: 指定來源 image 中的檔案路徑
    * **rw or readwrite**: 允許改寫

    **Example**
    ```dockerfile!
    # 從 php:alpine 映像中將 /usr/local/bin/docker-php-entrypoint 檔案掛載到容器中的 /docker-php-entrypoint 位置，並使用 cat 命令將該檔案的內容輸出到標準輸出中。

    RUN --mount=type=bind,from=php:alpine,source=/usr/local/bin/docker-php-entrypoint,target=/docker-php-entrypoint cat /docker-php-entrypoint
    ```

* **cache**: 將緩存掛載到容器的指定路徑上，當容器中需要頻繁存取時可以從快取中存取而不需要重新 build
    **options**
    * **id**: 設定 volume ID/名稱
    * **target**: 掛載到容器的目標路徑
    * **ro or readonly**: 唯讀
    * **sharing shared|private|locked**
        **options**
        * **shared**: 分享給所有人
        * **private**: 創建一個新的 volume 掛載給別人，不會動到原始的 volume
        * **locked**: 建置期間不會被其他容器或建置工作共享
    * **from**: cache 來源基於哪個 build 階段，表示它會使用前一階段的結果作為輸入，而不是從頭開始建置。
    * **source**: 從哪個路徑複製到 cache 中的 target 設定的路徑

    **Example**
    ```dockerfile!
    # 建立一個名為 my_app_npm_module 的 volume，並掛載到 Docker 映像中的 /app/node_modules 目錄。

    RUN --mount=type=cache,target=/app/node_modules,id=my_app_npm_module,sharing=locked

    # sharing=locked 指定這個 volume 在建置期間是被鎖定的，這表示它在建置期間不會被其他容器或建置工作共享。
    ```

* **secret**: 用於一些需要登入才能取得的資源，在最終的生成物中也不會保留這些敏感資訊
    **options**
    * **target**: 掛載到容器的目標路徑
    * **id**: 設定 volume ID/名稱
    * **required=true|false**: 設置true 表示這個檔案是必須存在或是可用的，否則會 error
* **tempfs**: 將一個 tmpfs 檔案系統掛載到指定位置
    **options**
    * **target**: 掛載到容器的目標路徑
    * **size**: 設置檔案最大容量
* **ssh**: 直接使用本地的ssh-agent 去做登入認證
    **options**
    * **target**: 掛載到容器的目標路徑
    * **id**: 設定 ssh key ID
    * **required=true|false**: 設置true 表示這個檔案是必須存在或是可用的，否則會 error

---

## docker compose
> 用於定義和執行多容器 Docker 應用程式的工具，利用 YAML 檔，向 Docker API 發送指令，再由 API 把指令內容傳遞給 Docker 應用程式。

## docker compose 指令
* **docker compose `[opionts]` [command]**:
    **options**
    * **-f or -\-file fileName**: 指定 docker compose 檔案
    * **-p or -\-project-name name**: 指定 container 前贅字，用於分辨是哪個 project 的容器

* **docker compose up**: 建立並啟動所有 service，並且在背景執行，相當於 create + start，首次啟動會創建新容器，但若容器已存在且為停止狀態，並不會重新創建新容器，而是啟動存在的容器。
    **options**
    * **-d or -\-detach**: 在背景運行
    * **-\-scale**: 擴展 service 容器數量
    * **-\-dry-run**: 以模擬的方式執行，不會真實啟動相關的容器 (用於檢查容器啟動的情況)
    * **-\-build**: 強制重新 build images (當 Dockerfile 或 Compose 檔案有更改，就會用到這個指令)
    * **-\-no-build**: 不自動 build 缺少的 service image
    * **-\-no-recreate**: 如果容器已經存在了，則不重新創建
    * **-\-force-recreate**:  強制重新建立容器
    * **-\-no-deps**: 不啟動服務所連結的容器
    * **-\-no-color**: 不使用顏色區分不同服務的 console 輸出
    * **-t or -\-timeout**: 停止容器時的超時設定(預設為 10s)
        ```javascript!
        docker-compose up --scale service_name=number
        ```
* **docker composr scale serviceName=num**: 指定特定的服務要啟動幾個容器來執行
* **docker compose stop**: 停止所有容器，被停止的容器不會佔用 CUP，但在檔案系統內仍是存在的
* **docker compose start**: 重啟所有系列容器
* **docker compose restart**: 重啟服務 (常用於修改了 Docker Compose 文件時)
* **docker compose down**: 停止 compose 檔中的所有服務容器，並刪除容器、網路等 (不刪除 volumes)
    **options**
    * **-v or -\-volumes**: 刪除 volumes  
* **docker compose kill**: 發送信號終止容器
    **options**
    * **-s signal**: 發送指定訊號
* **docker compose rm**: 刪除所有停止狀態的容器
    **options**
    * **-d or -\-force**: 強制刪除
    * **-s or -\-stop**: 暫停容器
    * **-v or -\-volumes**: 刪除容器掛載的 volume
* **docker compose pause**: 暫停容器
* **docker compose unpause**: 恢復容器運作
* **docker compose pull imageName**: 拉取 compose檔案中定義的 service 所需要的映像檔
    **options**
    * **-\-ignore-pull-failures**: 忽略拉取過程中的錯誤
* **docker compose push**: 將 compose 中 servie 建構出的 image 推送到 repository
* **docker compose run serviceName**: 相當於 docker run，在指定 service 上運行一次性的命令
    **options**
    * **-d or -\-detach**: 後臺運行模式
    * **-\-name**: 幫容器取名字
    * **-\-entrypoint cmd**: 覆寫 entrypoiont 指令
    * **-e key=value**: 設定環境變數
    * **-u or  -\-user="userName"**: 設定容器的使用者名稱或UID
    * **-\-rm**: 運行後自動刪除容器
    * **-p or -\-publish=[port]**: 映射 port 到本機
    * **-P or -\-service-ports**: 啟動所有服務的 ports 並映射到本機
    * **-T or -\-no-TTY**: 不分配 pseudo tty，所以需要靠 tty 的指令都無法運作
    * **-\-no-deps serviceName**: 不會啟動指定 service 內相關的容器
* **docker compose build**: 用於重新建構 image 
    **options**
    * **-\-no-cache**: build 過程中不使用 cache
    * **-\-pull**: 每次都嘗試使用最新版本的 image
* **docker compose ps** : 列出 compose 中所有執行中的容器
* **docker compose ls** : 列出 compose 中所有執行中的容器，同上方指令
* **docker compose images**: 列出 compose 檔案中包含的 images
    **options**
    * **-q or -\-quiet**: 只顯示 image id
* **docker compose watch**: 啟動在 compose file 中定義的 watch action
* **docker compose logs serviceName**: 查看 container 的標準輸出內容
    **options**
    * **-\-no-color**: 單色輸出
    * **-f or -\-follow**: 即時查看 log
    * **-t or -\-timestamps**: 顯示時間戳
    * **-\-tail=num**: 從最尾端開始顯示
* **docker compose top serviceName**: 查看服務容器內執行的程序
* **docker compose port**: 列印容器 port  的對應資訊
    **options**
    * **-\-protocol=proto**: 指定連接埠協議 (tcp<default>、udp)
    * **-\-index=index**: 用於多容器的情況下，指定命令容器的序號
* **docker compose version**: 列印版本資訊
* **docker compose alpha viz**: 繪製容器關係圖 (實驗性功能)
    **Example**
    * **|** : 將前一個指令的輸出作為後一個指令的輸入
    * **dot -Tsvg**:  raphviz 工具的 dot 指令，Tsvg 選項指定輸出格式為 SVG
    * **\> output.svg**: 將產生的 SVG 映像儲存到名為 output.svg 的檔案中
    ```dockerfile!
    docker compose alpha viz | dot -Tsvg > output.svg
    ```

## docker compose file
> 在 VS code 中可透過 ``docker init`` 來快速建立 DockerFile，或是手動建立 ``compose.yaml`` 檔案


[Docker compose file DOC](https://docs.docker.com/compose/compose-file/compose-file-v3/#devices)

## version
> 表示正在使用 Docker Compose 設定檔版本

---

## Services
> 定義要運行的容器

**Example**
```dockerfile!
Services:
  servicename:
```

### image
> 定義容器下的映像檔

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
```

### container_name
> 定義容器建立後的名稱

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    container_name: db
```


### working_dir 
> 代表指令 -w or --workdir

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    working_dir: /src
```

### volumes
> 代表指令-v or --volume 定義存放資料的地方

**表達方式**
* **volumes: hostPath:containerPath**: bind mount
* **volumes: volumeName:containerPath**: volume mount
* **volumes: volumeName:containerPath:ro**: 表示唯讀 `:ro`


**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
      # hostPath:containerPath
```

### command
> 啟動 container 時會執行的指令

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    command: endor/bin/phpunit
```

### build
> 讓 docker-compose 使用自己定義好的 Dockerfile

**options**
* **context**: 定義 dockerfile 的位置
* **dockerfile**: 你 dockerfile 的名稱
* **args**: build 過程中存取的環境變數
* **network**: 設定網路容器連接，none表示停用網絡
* **cache_from**: 指定緩存來源
* **target**: 表示從 DockerFile 中的哪個階段開始 builds

**Example**
```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
      cache_from:
        - alpine:latest
      target: prod
```

### entrypoint
> 指定啟動 container 時會執行的指令或是腳本

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    entrypoint: /app/start.sh
```

### ports
> 將容器連接埠資訊與主機映射

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
     - 5432:5432
     # hostPort:containerPort
```

### expose
> 暴露容器內部的 port，但不映射到主機，只被連接的容器ㄋ存取

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    expose:
     - "3000"
     - "8000"
```

### networks
> 將每個容器都設定相同的 networks，讓容器溝通

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
     - 5432:5432
    networks:
     - net
```

### network_mode
> 設定網路模式

**options**
* **bridge**: docker 預設的 bridge 網路
* **host**: 容器與主機共享網路，可直接存取主機 port
* **none**: 僅用容器的網路
* **service:[service name]**: 使用指定服務的網路，使容器之間可直接溝通
* **container:[container name/id]**: 使用指定容器的網路，使容器之間可直接溝通


**Example**
```dockerfile!
version: '3.8'

services:
  web:
    image: nginx
    network_mode: bridge
```


### external_links (不建議使用)
> 連接到此 compose.yaml 檔外的容器，或並非 compose 管理的外部容器

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    external_links:
     - redis_1
```

### extra_hosts
> 對服務添加額外的 host 名稱及 IP 位置映射，對於容器內須訪問其他 host 時非常有用

**Example**
```dockerfile!
version: '3.8'

services:
  web:
    image: nginx
    extra_hosts:
      - "example.com:192.168.1.100"
      - "test.example.com:192.168.1.101"
```

### dns
> 自訂 DNS 伺服器

**Example**
```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    dns: 8.8.8.8

dns:
  - 8.8.8.8
```


### dns_search
> 配置 DNS 搜尋域

**Example**
```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    dns_search: example.com

dns_search:
  - domain1.example.com
  - domain2.example.com
```

### domainname
> 設置容器的網域名稱

**Example**
```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    domainname: your_website.com
```

### hostname
> 設置容器的主機名稱，讓容器在網路中能識別自己幫助溝通

**Example**
```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    hostname: test
```

### mac_address
> 設置容器的 MAC 位址

**Example**
```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    mac_address: 08-00-27-00-0C-0A
```

### environment
> 設定建立容器時環境變數帶入的值

**Example**
```dockerfile!
 postgres:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
     - 5432:5432
    networks:
      - net
    environment:
      - POSTGRES_DB=Foo_production
      - POSTGRES_USER=Foo
      - POSTGRES_PASSWORD=Foobar
```

### env_file
> 從檔案中取得環境變數

```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    #env_file: .envFilePath
    env_file: .env 
    
```

### secrets
> 存放一些敏感資訊

**options**
* **external=true|false**: 是否由外部提供
* **file: filePath**: 從檔案中讀取


```dockerfile!
version: '3.7'

services:
  my_service:
    image: my_image
    secrets:
      - my_secret
    environment:
      - DB_PASSWORD_FILE=/run/secrets/my_secret

secrets:
  my_secret:
    external: true
```

### security_opt
> 設定容器的標籤來限制容器對系統資源的存取權，在 docker swarm mode 中會被忽略


```dockerfile!
version: "3.8"
services:
  my_service:
    image: my_image
    security_opt:
    # 指定容器運行時用戶為 user1
      - label:user:user1
    # 指定容器運行時腳色為 web_server
      - label:role:web_server
```

### stdin_open 
> 代表指令 -i or --interactive，表示開啟標準輸入，可接受外部輸入

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    stdin_open: true
```

### tty 
> 代表指令 -t or --tty，模擬一個偽終端

**Example**
```dockerfile!
services:
  postgres:
    image: postgres:16-alpine
    tty: strue
```

### cap_add
> 指定容器擁有的能力

**Example**
```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    cap_add:
        - ALL
```


### cap_drop
> 去掉容器擁有的能力

**Example**
```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    cap_drop:
      - NET_ADMIN
```

### pid
> 指定容器的 PID 命名空間

**options**
* **host**: 與主機共享 PID 命名空間
* **container:<name|id>**: 與指定容器共享 PID 命名空間

**Example**
```dockerfile!
version: '3.8'

services:
  web:
    image: nginx
    pid: "host"
```

### deponds_on
> 設定容器之間的相依性，決定容器的啟動順序

**Example: 表示要先啟動 postgres**
> 並不會等待完取啟動 postgres 才啟動 rails

```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - 3000:3000
    networks:
      - net
    depends_on:
      - postgres
```

### develop
> 設定 watch mode

**options**
* **action**: 要做什麼事
    * **sync**: 相當於 hot-reload
    * **rebuild**: 重新 build image 並且將現在的容器 image 替換成重新 build 的 image
    * **sync+restart**: 同步檔案並且重啟容器，通常可用於 config file change (不需要重新 build image)
* **path**: 主機中的檔案路徑
* **target**: 控制檔案要 map 到容器內哪個路徑下
* **ignore**: 要忽略的檔案

**Example**
```dockerfile!
services:
  # frontend service
  web: 
    depends_on:
      - api
    build: ./frontend
    ports:
      - 5173:5173
    # docker compose watch mode
    develop:
      watch:
        - path: ./frontend/package.json
          action: rebuild
        - path: ./frontend/package-lock.json
          action: rebuild
        - path: ./frontend
          target: /app
          action: sync
```

### devices
> 將主機上的設備映射到容器

**Example**
```dockerfile!
services:
    develop:
      watch:
        - path: ./frontend/package.json
          action: rebuild
      devices:
        - "/dev/ttyUSB0:/dev/ttyUSB0"
```

### stop_signal
> 發送訊號觸發容器停止訊號

**Example**
```dockerfile!
version: '3.8'
services:
  my_service:
    image: my_image
    stop_signal: SIGUSR1
```

### restart 
> 重新啟動，通常用於確保容器在故障或異常情況下能夠恢復正常運行。
**options**
* **no**: default，不會自動重新啟動容器
* **always**: 無論手動停止 (stop) 還是將服務停止 (down)，還是將服務停止，確保即使發生故障，容器也可以重新啟動
* **on-failure**: 當容器因為失敗而停止時，才會自動重新啟動
* **unless-stopped**: 類似 always 類似，但若是手動停止，並不會自動重啟

**Example**
```dockerfile!
version: '3.8'
services:
  my_service:
    image: my_image
    restart: always
```

### privileged
> 啟用特權模式，擁有對主機系統的全部存取權

**Example**
```dockerfile!
version: '3.8'
services:
  my_service:
    image: my_image
    privileged: true
```

### read_only
> 指定容器中的檔案系統是否為唯讀模式

**Example**
```dockerfile!
version: '3.8'
services:
  my_service:
    image: my_image
    read_only: true
```


### tmpfs
> 掛載一個 tmpfs 檔案系統到容器

```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    tmpfs: /run
    
tmpfs:
  - /run
  - /tmp
```


### cgroup_parent
> 指定父 cgroup 群組，表示繼承群組的資源限制

**Example**
```dockerfile!
services:
  rails:
    build:
      context: .
      dockerfile: Dockerfile
    cgroup_parent: cgroups_1
```

### deploy
> 僅適用於 swarm mode

### configs
> 僅適用於 swarm mode

### healthcheck
> 透過指令檢查容器運作是否正常
**options**
* **test**: 檢查指令
* **interval**: 定義間隔多久檢查一次
* **timeout**: 定義檢查最長等待時間
* **retries**: 檢查失敗時重新嘗試幾次


**Example**
```dockerfile!
version: '3.8'

services:
  web:
    image: nginx
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
```

### labels
> 新增一些輔助說明資訊

**Example**
```dockerfile!
version: '3.8'

services:
  web:
    image: nginx
    labels:
      - "com.example.description=Web Server"
      - "com.example.department=IT"
      - "com.example.environment=Production"
      - "com.example.owner=John Doe"
```

### user
> 指定容器運行時使用者的名稱，使用者必須存在於容器中

**表達方式**
* **user: userName**
* **user: "UID:GID"**

**Example**
```dockerfile!
version: '3.8'
services:
  my_service:
    image: my_image
    user: "1001:1001"
```

### logging
> 配置容器 log 紀錄的選項

**options**
* **driver**: log 紀錄驅動程式
    **options**
    * **json-file**: 儲存成 json 格式
    * **syslog**: 將記錄發送到 syslog 服務中
    * **none**: 不紀錄
* **max-size**: 單個記錄檔最大容量上限
* **max-file**: 最多保留幾個紀錄檔案

**Example**
```dockerfile!
version: '3.8'

services:
  web:
    image: nginx
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

### sysctls
> 配置容器核心參數

**Example**
```dockerfile!
version: '3.8'
services:
  my_service:
    image: my_image
    sysctls:
      - net.core.somaxconn=1024
      - net.ipv4.tcp_syncookies=1
```


### ulimits
> 指定容器資源限制，像是 CPU 時間、檔案描述等

**options**
* **nproc**: 容器最大 process 數量
* **nofile**: 容器中開啟的文件字數上限
* **soft**: 軟限制 (超過會警告)
* **hard**: 硬限制 (超過會報錯)


**Example**
```dockerfile!
version: '3.8'
services:
  my_service:
    image: my_image
    ulimits:
      nproc: 65535
      nofile:
        soft: 1024
        hard: 2048
```
---
### compose 讀取變數
> 支援動態讀取主機的系統環境變數和當前檔案中.env的變數

**Example**
```dockerfile!
version: "3"
services:

db:
  image: "mongo:${MONGO_VERSION}"
```

---
## volumes
> 相當於 docker volume create

**Example**
```dockerfile!
services:
 mysql-server:
    build:
      context: ./mysql
    image: mysql-image:latest
    container_name: mysql-contianer
    volumes:
      - mysqlVolumes:/var/log
      
volumes: 
 mysqlVolumes:
   name: mysql_volumes
```

--- 
## networks
> 相當於 docker network create

**options**
* **driver**: 網路驅動程式
* **config**: 網路配置
    * **subnet**: 設定子網路

**一般配置**

```dockerfile!
services:
  proxy:
    build: ./proxy
    networks:
      - frontend
networks:
  frontend:
    # Use a custom driver
    driver: custom-driver-1
```

**IPAM 配置**
```dockerfile!
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    networks:
      - app_net

networks:
  # network name  
  app_net:
    # 指定 IP 位置管理配置 ( 負責管理 docker container 的 IP 位置 )
    ipam:
      # 指定驅動程式
      driver: default
      # 網路配置
      config:
        - subnet: "172.16.238.0/24"
        - subnet: "2001:3984:3989::/64"
```

---

## docker hub
> 存放 docker image 的地方
### 個人映像檔名稱
* **user name**: 使用者名稱
* **repo name**: repository 名稱
* **tag name**: 分辨同一個 repository 中的不同映像檔
```javascript!
<user name>/<repo name>:<tag name>
```

### docker hub 指令
* **docker login/logout**: 登入/出 docker
> 一般使用Docker的映像檔時，是完全不需要帳號密碼的，只有要上傳映像檔才需要。

* **docker search imageName**: 尋找特定 image
