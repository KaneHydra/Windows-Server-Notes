---
id: IIS
aliases: []
tags: []
---

# IIS

## 連線到 IIS Server 的網頁

之後在 Win10PC 登入 domain administrator  
然後開啓 IE (edge 被停用了)  
連到 剛剛安裝IIS Server 的Member Server 192.168.8.2  
就可以開啓 IIS 預設網頁

> 改IIS設定的時候記得都要選下面的IIS Server,不能選上面的，否則會改到整個Server的設定

## 新增IIS網頁

IIS 管理員的 `站台`

`預設文件` 可以 查看預設的首頁文件檔名  
比如說 `Default.htm`  
`iisstart.htm` 是預設  
也可以點右邊的`新增`按鈕  
把你的首頁`檔案全名包含副檔名`輸入進去就可以讀到  
因爲他會按照順序搜尋  
所以把你的網頁調到最上面  
基本設定 => 編輯站臺 => 實體路徑 改成你網頁資料夾的路徑  
就可以抓到在你的網頁資料夾下面的網頁檔案

## 限制特定使用者訪問網站

`IP位址及網域限制` => 開啓後右邊 => 新增拒絕項目  
輸入要拒絕的 IP, 他就不能訪問

## 驗證

預設是:  
匿名驗證:停用  
Windows驗證:啓用  
這樣瀏覽網頁不用登入  
如果改成相反,就需要輸入domain user的帳號密碼才能查看網頁

domain user 要在 Domain Controller Server 新增使用者  
IIS server 上面的 domain member 帳號也可以存取

## 使用 IIS server 的 local user account 訪問

IIS server 的電腦名稱是 server2  
在這裏新增: server2 => 工具 => 電腦管理 => 本機使用者和群組  
瀏覽網頁時,訪問`192.168.8.2`  
重開或登出win10pc1後  
就能使用 server2 的 local user 訪問  
沒有加入 domain 的 server 就要這樣訪問

## IIS Server 編輯站台繫結

沒架 IIS Server 的時候, 在這裏做 ip 名稱解析  
比如說 localhost 對應到 127.0.0.1  
`C:\Windows\System32\Drivers\etc\host`

站臺繫結未指定的情況,  
才能使用 localhost 或 127.0.0.1 連到網站做測試

這裏要注意不能使用 https, 會連不到  
總之192.168.8.2前面不要加任何東西

關閉防火牆才能ping到ip

> 停用所有使用者的螢幕保護裝置  
> `gpedit.msc`  
> 使用者設定 => 系統管理範本 => 控制台 => 個人化  
> 啓用螢幕保護裝置 => 已停用

## 實體目錄與虛擬目錄

-   實體目錄:
    -   主目錄底下的資料夾, 檔案總管看到的資料夾
-   虛擬目錄:
    -   外部目錄的別名
    -   可以隱藏實際網頁檔案的完整名稱
