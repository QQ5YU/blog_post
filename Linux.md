---
title: Linux 筆記
date: "2023-12-05"
tags: linux
---

# Linux 指令
### Linux 規則
* 大小寫分明
* 資料夾路徑符號: /
    > 在 win 中，資料夾路徑使用 \ 做區分，在 Linux 中則是使用 /。

### apt
> linux 中的套件管理工具

* **apt install packageName**: 下載套件
* **apt list**: 列出所有可用的安裝包 (包含可用與可安裝的)
    **options**
    * **-\-installed**: 僅列出已安裝的
* **apt update**: 更新資料庫中的套件，會從網路上抓一些較新的套件提供下載
* **apt upgrade**: 更新已安裝的 package
    **options**
    * **-y**: 預設回答 yes 來更新套件
* **apt remove packageName**: 會刪除指定的軟體包，但會保留相關的設定檔案
* **apt purge**: 刪除指定的軟體包及其相關的設定檔案

### dbkg
> Debian 安裝包管理器，用於安裝、刪除、管理 debian 安裝包

**options**
* **-i**: 安裝的意思

### 遠端操作電腦
> ssh，Security Shell。可以使用 CLI 透過加密的網路傳輸協定遠端操作電腦。

#### ssh user@ip
* **user**: 遠端電腦的帳號
* **ip**: 遠端電腦的位置
* **-p**: 指定通訊埠(port)

### 系統相關指令
* **info**: 列出和系統相關的資訊，如映像檔數、Container數、檔案系統目錄、Linux核心版本，使用Linux版本、CPU及記憶體等。
* **docker version**: 列出目前Docker的版本
* **lsns**: 列出 namespace 的訊息
    **options**
    * **-t string**: 過濾 namespace
* **unshare**: 創造新的 namespace
    **options**
    * **-\-pid**: 創造一個 PID 的 namespace
    * **-\-fork**: 在新的 namespace 中創建一個子 process，將在這個 process 中執行指令
    * **-\-mount-proc**: 將新的 namepace 中mount 一個 /proc 檔案系統 (/proc 為一個虛擬檔案系統，提供關於系統核心及正在執行的 process 資訊) 
* **mount**: 掛載文件系統的命令
    **options**
    * **-t type**: 指定文件系統類型
    * **-o options**: 指定掛載選項
    * **-r**: 唯讀
    * **-w*: 可讀可寫
    * **-n**: 不紀錄(不更新 etc/mtab 檔案)
    * **-a**: 掛載/etc/fstab 檔案中列出的所有檔案系統
    * **-\-make-shared filePath**: 創建一個共享的 subtree
    * **-\-make-private filePath**: 私有掛載，不與其他掛載點共享，代表對此掛載點操作並不會影響其他 namespace 的掛載點

### 基本感覺不太會用到的指令
* **whoami**: 顯示目前執行該指令的使用者資訊
* **who**: 顯示當前登入系統的用戶資訊
* **passwd**: 更改密碼
* **history**: 列出先前所下的指令
    > **!+history 中列表的數字**: 重複幫你輸入一次那個指令
    ![example image](https://hackmd.io/_uploads/SJqiT2OtT.png)

### 快捷鍵
* **ctrl + C**: 中斷當前運行的程序或命令
* **ctrl + D**: 結束輸入或退出終端機
* **ctrl + Z**: 將前台運行的指令放置後台執行，代表該 process 會被暫停執行
* **ctrl + insert**: 複製
* **shift + insert**: 貼上
* **ctrl+shift+p**: 貼上(若是貼上指令，不會自動執行)

### 檔案系統

| 路徑 | 說明 |
| :--------: | :--------: |
| /root/     | root 的家目錄   |
| /home/    | 一般使用者的家目錄位置   |
| /proc/    | 存放一些系統資訊的檔案   |
| /etc/   | 各種設定檔   |
| /tmp/     | 各種暫存檔   |

**常見目錄表示**
* **/**: 表示根目錄
* **~**: 表示 /home/<使用者名稱>/ ，也就是家目錄

### 權限
> 透過指令 ls 可以了解資料夾內的檔案或資料夾的權限列表，以及檔案所屬群組，與檔案擁有者

![image](https://hackmd.io/_uploads/B1J7Wg5iT.png)

### 操作符號

| 符號 | 說明 |
| :--------: | :--------: |
| :  | 指令終止符號，無論前面指令是否失敗都會繼續執行下一個  |
| & | 讓符號前的指令在背景執行  |
| &&   | 指令連接符號，第一個指令成功才執行下一個  |
| \| | 指令連接符號，將前項指令輸出當成下一個命令輸入  |
| \|\|  | 前一個命令失敗時執行下一個命令  |
| \`\`  | 讓符號內的指令先執行 |
| > | 重導向符號，可以將輸出複寫至檔案  |
| >> | 重導向符號，可以將輸出從檔案結尾繼續加入 |
|FDnum>&FDnum|將 FDnum 重新定向到 FDnum (EX. 2>&1 將 STDERR 重定向到 STNIN)|

### 表達式子
|符號|說明|
|:--:|:--:|
|* |全部|
|?|一個字元|
|{}|展開的序列|

#### 正規表達式
|符號|說明|
|:--:|:--:|
|\s |空白字元(含tab)|
|\d|數字|
|\w|數字+英文字母+底線(\_)|
|. |表示一個字元(字母、數字、符號)|
|?|比對前一個字串或不比對|
|\*|比對前一個字串 0~n 次|
|+|比對前一個字串 1~n 次|
|\(\)|分組 (Ex. sy(m|n) 會比對到 sym 及 syn)|
|\[\]|比對任意字串的每個項目 (Ex. apple[1234] 會比對到 apple1~apple4)|
|-|一段連續字符 (Ex.apple[1-4])|


**定位符號**
> 不會被比對

|符號|說明|
|:--:|:--:|
|^|以...開頭(Ex. a^ 代表以a開頭)|
|$|以...結尾(Ex. b^ 代表以b 結尾)|


**邏輯比對**

|符號|說明|
|:--:|:--:|
|\|| OR 的意思|

**數量表示**

|符號|說明|
|:--:|:--:|
|\{}| 數量符號|
|\{10}|表示 10 個字|
|\{10,}| 表示 10個字以上|
|\{1,5}|表示 1-5 個之間|



#### 查找檔案

* **\*.txt**: 找副檔名是.txt的檔案
* **\*.??**: 找副檔名是兩個字元的檔案
* **a.{js,html,css}**: 會輸出成 `a.js` `a.html` `a.css`



### 變數使用方式
#### 宣告
```bash!
VARIABLENAME=VARVALUE
```
#### 呼叫
```bash!
ECHO $VARIABLENAME
```
#### 刪除變數
```bash!
unset VARIABLENAME
```

### 基本指令
* **ps**: 表示 process status，用於顯示 process 狀態
    **options**
    * **aux**: 代表 "all processes" 和 "user-oriented output"，表示顯示所有用戶的所有 process
    * **-e**: 顯示系統中的所有 process
    * **-f**: 顯示完整的 process 資訊，包含 process 之間的關係
    * **-a**: 列出所有當前使用者的進程
    * **-A**: 列出所有系統中的進程
    * **-o**: 自定義要顯示的資訊，以逗號分隔 (pid,ppid,stat,comm)
* **ls**: (list) 列出工作路徑下的檔案或是目錄清單
    **options**
    * **-a**: 顯示所有文件(包含隱藏文件)
    * **-l**: 表示使用長格式列印訊息
    * **-al**: 顯示詳細資料輸出詳細的列表以及隱藏文件
    * **-R**: 顯示現在位置的所有子檔案和資料夾
    * **-1**: 轉換排列 (水平變垂直)
    * **-h**: 以人類可讀的方式顯示
* **cd**: Changing Directories，切換工作路徑
    **options**
    * **~**: 切換到 home 資料夾
* **mkdir**: 建立路徑 (資料夾)
    **options**
    * **-p**: 遞迴創建，表示資料夾不存在會自動創建
* **pwd**: print working directory，列印當前路徑
* **rm**: 刪除檔案
    **options**
    * **-i**: 啟動交互模式，會提示使用者在刪除每一個檔案或目錄之前進行確認
    * **-d**: 刪除空資料夾，如果資料夾有東西會不能刪除
    * **-r**: 刪除整個資料夾
    * **-f**: 強制刪除
    * **fileName***: 表示從fileName 後的檔案都要刪除
* **rmdir**: 刪除空資料夾，如果資料夾有東西會不能刪除
    **options**
    * **-p**: 當子資料夾被刪除後使它也成為空資料夾的話，則順便一併刪除
* **cp fileName newFileName | directory**: 複製檔案，重新命名為 newFileName，或是儲存到特定路徑下
    **options**
    * **-r**: 複製資料夾
* **mv**: 移動或重新命名檔案、目錄
* **touch**: 建立檔案
* **grep**:
    **options**
    * **keyword**: 查詢特定關鍵字
    * **keyword fileName**: 在指定檔案中查找特定關鍵字
    * **-A 行數**: 顯示指定行數的前一行
    * **-B 行數**: 顯示指定行數的後一行
    * **-c or -\-count**: 不會顯示行數，直接顯示匹配到的行數
    * **-C 行數**: 顯示指定行數的前一行及後一行
    * **-d or -\-directories=<路徑>**: 指定要搜索的目錄
    * **-e or -\-regexp=\<pattern>**: 指定一個或多個搜索模式（字符串）作為參數。這些模式將被視為單獨的參數，而不是一個整體的正則表達式。
    * **-E or -\-extended-regexp**: 指定一個擴展的正則表達式，可使用一些政則表達式的邏輯運算等等
    * **-f or -\-file=fileName**: 指定規則文件
    * **-n or -\-line-number**: 顯示第幾行
    * **-i or -\-ignore-case**: 不在乎大小寫搜尋
    * **-r or -\-recursive**: 找尋資料夾底下的所有檔案
    * **-l**: 只列印符合的檔名
    * **-v or -\-invert-match**: 反向查找，排除的意思
    * **-w or -\-word-regexp**: 顯示整個單字符合的欄位
* **find**: 搜尋指定的檔案
    **options**
    * **-iname**: 不在乎大小寫搜尋
    * **-name**: 要求大小寫相同
    * **-size**: 按檔案大小查找，表示大於(使用+)或小於(使用-)指定大小，單位可以是c（位元組）、w（字數）、b（區塊數）、k（KB）、M（MB）或G（GB）
    * **-mtime days**: 按修改時間查找，表示在指定天數前(使用+)或後(使用-)，days 是一個整數表示天數。
    * **-user userName**: 依照擁有者去找
    * **-group groupName**: 依照檔案所屬群組去找
    * **-exec command**: 在找到的每個檔案上執行指定的命令
    * **-delete**: 將找到的檔案都刪除
        **寫法**
        * **-name 'name'**: 找叫做 name 的檔案
        * **-name '\*a\*'**: 找包含 a 這個字的檔案
        * **-name 'A\*'**: 找 A 開頭的檔案
        * **-name '*A'**: 找 A 結尾的檔案
    * **-user**: 指定使用者
    * **-group**: 指定群組
    * **-perm**: 指定權限
    * **-type <d/p/f/l/s>**: 指定類型: 目錄、pipe、檔案、連結檔案、socket
    **常用方式**
    * find \<path> -iname \<fileName>: 搜尋指定路徑下的特定名稱檔案
    * find \<path> -iname '*.<副檔名>': 搜尋指定路徑下的特定副檔名檔案
* **sort**: 按字母排序
    **options**
    * **-b**: 忽略每行開頭的空格字元
    * **-c**: 檢查檔案是否已完成排序，不會對檔案排序(若檔案已排序，不會輸出內容，未排序則會輸出錯誤)
    * **-d**: 處理英文字母、數字及空格字元，忽略其他的字元
    * **-f**: 排序時，將小寫字母視為大寫字母
    * **-n**: 按照數字大小
    * **-u**: 去除重複值
    * **-r**: 反轉排序
    * **-t <分隔字元>**: 指定分隔字元
* **uniq**: 判斷上一行與下一行的值有無相同，相同則忽略只印出第一項(童常會搭配 sort 一起用，先排序後塞選值)
    **options**
    * **-c or -\-count**: 每列旁邊顯示該行重複出現的次數
    * **-d or -\-repeatede**: 僅顯示重複出現的行列
    * **-f**
    * **-u or -\-unique**: 只列出沒有重複值的值
* **cut**: 分隔字元
    **options**
    * **-f 欄位 or -\-field=欄位**: 指定要切割的欄位列表，可用逗號分隔數字或者範圍（例如：1-3）來表示。
    * **-d 分割符 or -\-delimiter=分割符**: 預設為 Tab，指定分隔符號
* **awk**: 處理文字檔案的文字分析工具
    **options**
    * **-F fs or -\-field-separator fs**: 指定字段分隔符號
* **file \<fileNameOrPath>**: 查看指定檔案的檔案類型
* **poweroff**: 關機
* **exit**: 離開
* **clear**: 清理指令介面
* **wc**: (word count) 用於統計字數、行數
    **options**
    * **-l**:  只統計行數
* **diff**: 比較兩個檔案或目錄之間的差異，若相同則不會輸出任何東西
    **opions**
    * **-u**: 以 Unified 格式顯示差異
    * **-r**: 遞迴比較
* **kill signal processNumber**: 發送 signal
    **options**
    * **-l**: 列出 signal 表
    **使用範例**
    * ex.kill -INT 3448，意思是發送 SIGINT(中斷訊號) 給 3448 號 Process
* **killall processName**: 殺死指定名字的所有 process
* **trap signal**: 用來捕捉訊號
* **man**: manual，指令查詢
    **options**
    * **-a**: 顯示所有符合的教學頁面
    * **-w**: 尋找特定指令的所在位置
    * **-aw**: 看第幾章節有指定的指令教學
* **history**: 查看以前使用過的指令
    **常見使用方式**
    * **搭配`!<command number>`**: 可指定要使用哪個指令
* **df**: 用來顯示檔案系統的磁盤使用情況的工具
    **options**
    * **-h or -\-human-readable**: 將磁盤大小以人類可讀的方式（例如GB、MB）顯示。
    * **-T or -\-print-type**:  顯示檔案系統類型
    * **-i or -\-inodes**: 顯示檔案系統的 inode 使用情況，即檔案和目錄的數量
    * **-x <副檔名>or-\-exclude-type=<副檔名>**: 排除指定副檔名的文件系統
* **du**: 顯示資料夾或是檔案大小
    **options**
    * **-a or -\-all**: 顯示資料夾中個別檔案大小
    * **-b or -\-bytes**: 顯示時以 bytes 為單位
    * **-c or -\-total**: 顯示個別大小以外也顯示總和大小
    * **-h or -\-human-readable**: 以人類可讀方式顯示
    * **-k or -\-kilobytes**: 以 1KB 為單位
    * **-m or -\-megabytes**: 以 1MB 為單位
    * **-s or -\-summarize**: 只顯示指定檔案或資料夾大小
    * **-X or -\-exclude-from=<排除的路徑或檔案名稱>**: 排除指定的資料夾或檔案
    * **-\-exclude=<資料夾或檔案名稱>**: 排除指定檔案
* **fdisk**: 管理磁碟分割表的命令列工具
    **options**
    * **-l**: 列出系統上所有磁碟的分割表資訊，包括磁碟的名稱、大小、分割數量等。
* **lsblk**: 用於列出 Block devices（塊裝置）資訊的 Linux 指令
    **options**
    * **-a or - \-all**: 顯示所有裝置，包括空的裝置
    * **-d or -\-nodeps**: 不顯示任何從屬裝置（subdevices）
    * **-p or -\-paths**: 顯示裝置的完整路徑
    * **-o or -\-output**: 指定要顯示的欄位
* **top**: 
    **options**
    * **-d**: 指定更新顯示的間隔時間，單位為秒
    * **-n**: 指定更新顯示的次數，次數一到就退出
    * **-u username**: 指定用戶名的進程資訊
    * **-p PID**: 顯示指定 PID 的進程資訊
* **htop**: (需要 install)顯示系統資源使用情況的互動式命令行工具
* **job**: 管理背景執行的 Process
* **fg [job_number]**: 將背景執行的 process 切換到前台運行
* **bg [job_number]**: 將暫停的 process 放到後台運行
* **sleep 秒數**: 暫停使用幾秒
    **options**
    * **&**: 後台運行

* **gzip fileName**: 壓縮檔案(副檔名為 .gz)
    **options**
    * **-d or -\-decompress or -\-uncompress**: 解開壓縮檔
    * **-f or -\-force**: 強制壓縮檔案
    * **-l or -\-list**: 列出壓縮檔案的相關資訊
    * **-r or -\-recursive**: 遞迴處理
    * **-k**: 保留原始檔案
    * **-t or -\-test**: 測試壓縮檔案是否正確無誤
    * **-q or -\-quite**: 不顯示警告訊息
    * **-v or -\-verbose**: 顯示壓縮比
* **gunzip fileName**: 解開被gzip 壓縮過的文件
    **options**
    * **-c**: 將解壓縮後的檔案內容輸出到標準輸出 (不是寫入檔案)
    * **-f**: 強制解壓縮
    * **-k**: 保留原始壓縮檔
    * **-l**: 顯示壓縮檔案的詳細信息
    * **-q**: 不顯示解壓縮進度和錯誤訊息
    * **-r**: 遞迴壓縮
    * **-t** : 測試壓縮檔案的完整性，不實際壓縮
    * **-v**: 顯示詳細的解壓縮訊息
* **tar**: 用於備份檔案
    **options**
    * **-c or -\-create**: 建立新的備份檔案
    * **-C pathName or -\-directory=pathName**: 切換到指定的目錄
    * **-f fileName or -\-file=fileName**: 指定備份檔案
    * **-k or -\-keep-old-files**: 解開備份檔案時，不會覆蓋現有的檔案
    * **-t or -\-list**: 列出備份檔案的內容
    * **-x or -\-extract or -\-get**: 從備份檔案還原
    * **-z or -\-gzip or -\-ungzip**: 透過gzip指令處理備份檔
* **alias varName='command'**: 設置快捷指令
    **重要觀念**
    * `'ls $pwd'`: 會依照你所呼叫的指令當時的情況去儲存變數
    * `"ls $pwd"`: 會依照你設定這個快捷指令時當下的環境去儲存變數
* **xargs**: 傳遞參數，用來組合命令
    **常見用法**
    * **command | xargs command**: 將前一個指令的輸出傳遞當作下一個指令的參數
* **ln file1 file2**: 創建檔案連結
    **重要觀念**
    * **硬連結**: 將兩個檔案指向同一個inode(檔案中的唯一識別符號)，兩個檔案存在同一個檔案系統中，原始檔案被刪除後資料依然存在，只要有一個硬連接指向這個 inode 資料就不會被刪除
    * **軟連結**: 產生一個特殊的檔案，擁有自己的 inode，該檔案的內容是指向另一個檔案的位置，此文件指向的檔案也擁有自己的 inode，可以跨越不同的檔案系統，如果原始檔案被刪除或移動，軟連結仍存在但指向的位置不存在，變成壞掉的軟連結
    **options**
    * **-s**: 軟連結
* **su userName**: switch user 變更為其他使用者的身份
* **pr**: 將文字檔轉換成適合列印的格式 (格式化)
    **options**
    * **-x**: 將資料分為 x column
    * **-t**: 去除上下邊界
    * **-d**: double space 輸出雙倍空格
    * **-n**: 顯示行號
    * **-h "title"**: 頁首加上標題
    * **-l num**: 設定頁面長度(行數)
    * **-o num**: 按邊號設定頁面格式
* **lp fileName**: 硬複製檔案
* **lpr fileName**: 硬複製檔案
### 權限指令
* **chmod <權限> <檔案名稱>**: change file mode bits，變更檔案權限

    **options**
    * **-R**: 修改資料夾權
    
    **數字表示法**
    * **0**: No Permission
    * **1**: Execute
    * **2**: Write
    * **4**: Read 
    
    **符號表示法**
    * **r**: 可讀
    * **w**: 可寫
    * **x**: 可執行
    * **+**: 新增
    * **-**: 移除
    
        
    **Example**
    > 擁有者(u) 新增(+) 讀取(r) 的權限
    ```shell
     chmod u+r new_file
    ```
* **chgrp \<groupName> fileName**: change group，更改檔案或目錄的群組（group），groupName 為指定群組名稱，fileName 為想更改的檔案或目錄名稱。
* **chown userName:groupName fileName**: (Changing Ownership and Group)，將指定文件（或目錄）的所有者更改為 userName，群組更改為 groupName
* **groups**: 查看目前使用者的group
* **newgrp groupName**: 切換群組
* **sudo**: 以 root 的身分執行該指令
    **options**
    * **-l**: 列出目前可以執行哪些指令
* **adduser**: 增加使用者
* **addgroup**: 增加群組
* **passwd 'username'**: 禁用使用者
    **options**
    * **-l**: 停止帳號使用
* **userdel**: 刪除使用者
    **options**
    * **-r**: 刪除使用者登入以及所有檔案等
* **usermod \<groupName> \<userName>**: 用於修改使用者帳戶的指令
    **optiions**
    * **-a**: append，在使用者現有的群組清單中加入一個新的群組
    * **-G \<groupName>**: 將使用者添加到哪個群組 
* **groupmod 按兩下 tab**: 檢視現有群組
* **deluser userName groupName**: 從指定群組中刪除指定使用者
* **finger**: 取得使用者的資訊 (要安裝)
    **options**
    * **-l**: 顯示詳細資訊(真實姓名、使用的 shell、登入時間等等)
    * **-m**: 不顯示真實姓名資訊
    * **-s**
    * **-p** 

### 網路服務指令
* **brctl**
    **options**
    * **show**: 查看 bridge 與連線資訊
    * **addbr bridgeName**: 創建 bridge


* **ip addr**
    **options**
    * **netNamespaceName**:  將指定的 IP 配置到指定的 bridge 上
* **ip link**
    **options**
    * **set dev bridgeName up**: 啟用 bridge
    * **list**: 列出 network link  的相關訊息
* **ip netns**
    **options**
    * **list**: 列出所有 network namespace
    * **add netNamespaceName**: 創建一個新的 network namespace
    * **delete netNamespaceName**: 刪除 network namespace
    * **exec netNamespaceName 指令**: 針對這個 netNamespaceName 執行指令
* **iptables**:用於配置 IPv4 防火牆的指令工具
    **options**
    * **-L**: 列出防火牆規則
    * **-\-line-numbers**: 顯示規則的編號
    * **-\-policy**: 設置防火牆規則
        **options**
        * **FORWARD**: 表示預設為轉發流量
        * **ACCEPT**: 接受轉發流量不過濾，允許通過防火牆
* **curl URL**: 常用來抓取某個網頁，或下載檔案
* **ping URL**: 用來測試連接到一個網頁的連線狀況



### 文字檔案處理
* **echo**: 用於字串輸出，跳脫符號 \
* **echo string > fileName.txt**: 將 echo 的文字存成一個檔案
* **cat fileName**: (concatenate) 主要用於顯示檔案的內容，也可以用來結合多個檔案內容
    **options**
    * **-n**: 顯示行數
* **cat file1.txt file2.txt > combined.txt**: 結合兩個檔案變成一個新檔案
* **more**: 分頁顯示檔案內容，按上下鍵可以換頁，用於內容多的檔案中
* **less**: 要先裝套件，很像 more 但主要是一行一行跑而不是以頁為單位
* **head**: 顯示檔案開頭內容
    * -n <行數>: 顯示幾行
* **tail**: 顯示檔案結尾內容
    * -n <行數>: 顯示幾行
* **wc fileName**: 統計文件中行數、字數、幾 bytes 等等
    **options**
    * **-l or -\-lines**: 只顯示行數
    * **-w or -\-words**: 只顯示單字數
    * **-c or -\-chars or -\-bytes**: 只顯示 bytes 數


### Signal
**種類**
* **SIGTERM**: 用來中止 process 的訊號，process 會處理完手邊的工作，優雅的關閉。
* **SIGINT(中斷)**: INT 是 Interrupt 的意思，通常會用來終止 Process，且 允許 Process 在終止前完成它所在執行的事情
* **SIGKILL(立即中止)**: 編號9，強制立即中止，資料容易有丟失風險
* **SIGTSTP(暫停)**: TSTP 是 Terminal Stop 的意思，他的功能就是暫停一個 Process
* **SIGCONT(繼續)**: CONT 是 Continue 的意思，讓 Process 繼續跑

[SIGNAL](https://man7.org/linux/man-pages/man7/signal.7.html)

#### 終止正在執行的程式
**SIGINT**: 中斷訊號，例如按下 ctrl + C ，Terminal 會發送了一個 SIGINT(中斷訊號) 給 Shell

#### 暫停正在執行的程式
**SIGTSTP**: 暫停訊號，例如按下Ctrl + Z，正在跑的 Process 會被暫停並且被 zsh 放到背景

#### 繼續執行被暫停的程式
**SIGCONT**: 繼續訊號，下指令 fg(foreground)，他會送一個 SIGCONT(繼續訊號) 給剛剛暫停的 Process，然後該 Process 就會被 zsh 叫到前景(foreground) 繼續跑

### 文字編輯器
#### vim

**編輯模式**
* **insert**
* **instead**

**常用指令**
* **w**: 存檔
* **q**: 離開
* **wq**: 存檔並離開


#### nano
> 字元終端機的文字編輯器

**常用指令**
* **y**: 儲存

**快捷鍵**
* **ctrl + x**: 退出

### tar
> 用於壓縮、解壓縮、備份檔案

**[tar 指令](https://blog.csdn.net/chunbokan8789/article/details/100898274?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170583084816800184142640%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=170583084816800184142640&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-100898274-null-null.142^v99^pc_search_result_base5&utm_term=linux%E4%B8%8Btar%E8%A7%A3%E5%8E%8B%E7%BC%A9%E6%8C%87%E4%BB%A4&spm=1018.2226.3001.4187)**



# Linux 系統知識

## OverlayFS 
> 一種聯合文件系統，將多個文件系統層堆疊再一起，docker 的 layer 概念

### whiteout
> overlay 文件系統中的一種特殊文件，用於表示被底層文件系統刪除但上層文件系統仍保留的文件或目錄(代表檔案不是真的被刪除)，**只能在底層中讀取，在上層中是讀取不到**

### opaque
> overlay 文件系統中的一種特殊文件，用於表示被上層文件系統刪除但底層文件系統仍保留的文件或目錄，**在底層中讀取不到，在上層中才能讀取**

## Mount namespace
> 用於隔離文件系統掛載點的 namespace，每個 process 都有自己的 mount namespace，代表不同 process 可以有不同的檔案系統


## shared subtree
> 將一個文件系統子樹(掛載點)在不同的 mount namespace 中共享，在其中一個 namespace 中對該 subtree 的掛載狀態做修改會影響到其他有共享的 namespace


## File Descriptor (FD)
> 在 Unix-like 作業系統中用於識別和存取檔案或資源的整數值。

**數值解釋**
* **0**: 輸入(STDIN)
* **1**: 輸出(STDOUT)
* **2**: 錯誤(STDERR)

# shell script

## 基礎語法

### 註解
**Example**
```shell!
# 單行註解

# 下方是多行註解方式
# approach 1
: '
多行註解第一種方式
'

# approach 2 EOF（End of File）
<< EOF
<< EOF 表示接下來的文本（直到遇到 EOF 為止）都將被當作一個整體來處理，並被視為一個字串
EOF

# approach 3 EOF（End of File）
: << EOF
這行指令告訴shell後面的內容應該被視為一個文本區塊的開始，這個區塊以EOF為結束標記。
EOF
```

### Shebang Line
> 腳本第一行的特殊註釋，用來指定要使用的 Shell

**Example**
```shell!
# 未指定則預設以$SHELL作為運行的shell
#!/path/to/interpreter
```

### print

**Example - 單行 print**
```shell!
#!/bin/bash
echo "Hello World"
```

**Example - 多行 print**
```shell!
#!/bin/bash
cat << STDIN
中間的文字將會被 print
Hello world ~~~
STDIN
```

### input
> 讀取輸入訊息並儲存成變數

**Example**
```shell!
#!/bin/bash

echo "Please enter your name: "
read name

echo "Hello, $name! Welcome to our script."
```

### 變數設定

* **宣告**: `KEY+VALUE`
    * **Array**:
        * `name=(Declare an array arr whose elements are separated by spaces)`
    * **ENV 設置**: 
        * `export KEY=VALUE`
* **存取**: `$KEY`
    * **Array**:
        * 獲取第n個元素(0-index): `${arr[n]}`
    * **存取指令運行結果**:
        * `var=$(command)`


### 基本運算

**基本語法**: `$(( expression ))`

**Example**
```shell!
result=$(( 5 + 3 ))
```

* **加減乘除**: `+-*/`
* **取餘數**: `%
* **var++ var--**: 指定變數後將變數加或減1
* **++var --var**: 將變數加或減1後指定變數
* **位元/邏輯反轉**: `!` or `~`
* **次方**:`**`
* **邏輯運算**
    * AND: `&&`
    * OR: `||`
* **三元運算子**: `expr ? exprTrue : exprFalse`

### 參數表達式

### 退出腳本
> 用於結束當前的 script，可指定一個數字作為退出碼。退出碼通常用於指示上一個命令的執行狀態，0表示成功，非零表示錯誤，可用來做一些邏輯判斷或錯誤處理
