---
id: SSL_CA_憑證發行
aliases: []
tags: []
---

# SSL CA 憑證 發行

在 Server1 安裝 AD 憑證服務,安裝視窗先不要關  
AD CS 角色服務要額外勾 `憑證授權單位網頁註冊`  
安裝完後,視窗上面有一行藍色字`設定目的地伺服器上的 Active Directory 憑證服務`可以點,  
點下去, 會開啟 AD CS 設定視窗,全部按下一步用預設值就好,不用改

安裝完成後還要設定 AD CS, 都安裝預設值就好

在 Server1 安裝 IIS 伺服器  
點 SERVER1 => 站台 => Default Web Site => CertSrv  
右邊點瀏覽應用程式 => `瀏覽*:80 (http)`  
會在瀏覽器開啟憑證服務

這裡點 下載 CA 憑證、憑證鏈結或 CRL => 下載 CA 憑證  
存到 `C:\cert\` 資料夾內  
然後開控制台, 搜尋 cert, 選 系統管理工具 下面的 管理電腦憑證  
左邊點 `受信任的憑證授權單位` => 右鍵 => 所有工作 => 匯入,  
選剛才儲存的憑證

在Server2上面,確定是用abc的administrator登入  
然後開IIS伺服器 => SERVER2 => 點中間的伺服器憑證  
現在沒有憑證  
點右邊的`建立憑證要求`  
一般名稱輸入`www.abc.com`  
中間4個空白欄位先隨便寫就好  
之後下一步,下一步,選擇私鑰的儲存位址  
存到`C:\WebRequest.txt`

先確認Server1 有裝 DC 且設定了 DNS  
主機`www`  
伺服器網域名稱`www.abc.com`  
IP位址`192.168.8.2`

Server2 開IE瀏覽器  
訪問 `192.168.8.1/certsrv`  
用 `abc/administrator` 登入  
要求憑證 => 進階憑證要求 => 選 用Base64 那個  
上面貼剛才儲存的 WebRequest.txt 的內容  
憑證範本選 Web伺服器  
提交

這裡在 Server1 上面開啟 `localhost/certsrc`  
如果要登入就用`abc/administrator`  
可以審核申請的憑證,以及看到發行了多少憑證

Server2上面如果申請憑證審核通過了,就可以下載憑證  
存到 `C:\cert\` 資料夾內  
然後在IIS伺服器 => SERVER2 => 點中間的伺服器憑證  
點右邊的`完成憑證要求`  
選擇剛才下載的憑證檔案,然後輸入一個名稱,確定即可  
這樣就可以在其他電腦上面訪問`https://www.abc.com`

在win10pc1訪問`https://www.abc.com`  
會跳安全性錯誤  
需要憑證  
這裡我們偷懶  
訪問 `192.168.8.1/certsrv`  
用 `abc/administrator` 登入  
這裡點 下載 CA 憑證、憑證鏈結或 CRL => 下載 CA 憑證  
存到 `C:\cert\` 資料夾內  
然後開控制台, 搜尋 cert, 選 系統管理工具 下面的 管理電腦憑證  
左邊點 `受信任的憑證授權單位` => 右鍵 => 所有工作 => 匯入,  
選剛才儲存的憑證  
成功更新後  
瀏覽器清除cookie後重開  
即可用憑證正常訪問`https://www.abc.com`
