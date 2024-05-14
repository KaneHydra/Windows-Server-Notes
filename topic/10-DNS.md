---
id: DNS
aliases: []
tags: []
---

# DNS

DNS Server

DNS 管理員

### 反向對應區域

右鍵=>新增區域=>主要區域

新增指標PTR  
把網域內電腦的 IP 跟 FQDN 都放進去  
這樣速度就快了

### 正向對應區域

一般都是新增正向對應區域  
升級DC後他會自己新建正向對應區域  
不過設定次要主機的時候要自己新增  
然後一開始的動態更新都要記得打勾

主要區域: 平常用的  
次要區域: read-only, 從其他伺服器備份過來的資料

### 快取檔案 cache.dns

### 主要區域 & 次要區域

可以提高速度的DNS Server 主要區域 & 次要區域  
設定完每個步驟都要重新整理,比較不會失敗

-   主要的 DNS Server1
    -   IP: 192.168.8.1
    -   主要區域: abc.com, Step1
    -   次要區域: Server3,192.168.8.3, Step2
-   次要的 DNS Server3
    -   IP: 192.168.8.3
    -   主要區域: Server3, Step3
        -   這裏新增完畢後, 在Server1 的 Server3 右鍵內容  
            => 允許區域轉送 => IP 輸入 192.168.8.1  
            => Server3 上面的 abc.com => 右鍵 => 從主機轉送新的區域副本
    -   次要區域: abc.com,192.168.8.1, Step4
        -   這裏新增完畢後, 在Server3 的 abc.com 右鍵內容  
            => 允許區域轉送 => IP 輸入 192.168.8.3  
            => Server1 上面的 Server3 => 右鍵 => 從主機轉送新的區域副本

### 新增主機

Server1 的abc.com 裏面右鍵 => 新增主機  
名稱輸入www  
IP輸入192.168.8.2  
建立指標打勾  
ftp的也一樣

如果一開始ip設錯,就先連線了  
導致後來重設ip後一樣連不上線  
就需要flush dns

```sh
# 需要管理員身份
# cmd:
ipconfig /flushdns
# powershell
Clear-DnsClientCache
```

### 新增主機別名 CNAME

上面ftp改用CNAME新增,新增時就不輸入ip,改用www.abc.com

### 同一臺電腦多個網站

1. 方法1: 一個站台對應一個IP
    - IIS Server
        - 乙太網路 => 內容
            - TCP/IPv4 => 進階=>
            - IP位址 => 新增沒人用的 ip, 比如說 192.168.8.201
        - 資料夾 => `C:\asp\mis\`,
        - 新增檔案 `mis.html`, 放一些網頁內容進去
        - IIS 管理員
            - 新增站臺
                - 站臺名稱 mis
                - 實體路徑 `C:\asp\mis`
                - 繫結 ip位址 192.168.8.201
            - 之後要改可以點左邊的 站台 => 右邊選 站台 => 右邊 繫結
            - 預設文件
                - 新增 `mis.html`
    - DNS Server
        - 正向對應區域 abc.com 新增主機
            - 名稱 mis
            - IP位址 192.168.8.201
            - 重新整理
    - 瀏覽網站
        - 192.168.8.201
        - mis.abc.com
2. 方法2: 不同站台相同IP, 但是用不同port number
    - IIS Server
        - 根據上面的設定修改
        - 新增站臺那裏的ip位址一樣用192.168.8.2
        - port 用 8081
    - DNS Server 不用動, 這個方法不會用到 網域名稱?
    - 瀏覽網站
        - `http://192.168.8.2:8081` 才能正確瀏覽
3. 方法3: 不同站台相同ip&port number, 直接用不同主機名稱
    - IIS Server
        - 根據上面的設定修改
        - IP 都用 192.168.8.2
        - port number 都用 預設的80
        - 主機名稱寫 mis.abc.com  
           因爲目前在 abc.com 網域  
           不同網站就改最前面的字
        - 一但輸入了主機名稱, 就無法使用 ip 瀏覽
        - 所以如果一臺Server只有一個站臺,建議不要輸入主機名稱
        - 如果還是可以看到...
            - 因爲 瀏覽器會自動抓上次連線的舊資料
            - 清除dns
            - 改IE設定 => 網際網路選項 => 設定 =>
                - 檢查儲存的畫面是否有較新的版本 => 每次造訪網頁時
    - DNS Server
        - IP 記得改一樣的
    - 瀏覽網站
        - mis.abc.com
