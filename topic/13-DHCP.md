---
id: DHCP_server
aliases: []
tags: []
---

# DHCP Server

## Server 端

在 Server2 安裝 DHCP Server  
工具開 DHCP  
選IPv4  
需要Admin授權

授權的2種方式:

1. 在左邊DHCP上面右鍵 => 管理授權的伺服器  
   授權 IP 輸入192.168.1.2  
   名稱 輸入FQDN名稱 server2.abc.com  
   這樣才能把ip租用給client端  
   授權完要重新整理

    > 要用 abc/administrator 登入才能授權, 不是 Server2/administrator

2. 在 server2.abc.com 上面右鍵授權,這樣就不用輸入 ip 跟 FQDN名稱  
   授權完要重新整理

新增一個領域,假設叫做8net  
起始ip記得避開現在已經使用的ip  
比如說我們使用  
192.168.8.101 ~ 192.168.8.200  
遮罩他自己會抓,用預設就好  
租用天數, 看 client 端數量定義  
如果ip充足, 可以設定時間長一點  
這裡為了教學需求,設定20分鐘

路由器  
default gateway 為 192.168.8.254  
新增

DNS Server 按照預設值  
立刻啟用領域

## Client 端

改成自動取得 IP 位址跟 自動取得 DNS Server

```sh
# 客戶端更新合約,取得新的ip
ipconfig /renew
# 客戶端釋放合約
ipconfig /release
```

釋放掉的 ip,  
如果沒有租用給別人  
通常再去租又會是同一個ip

## DHCP 伺服器的授權

-   未經授權的DHCP伺服器不會出租IP位址給用戶端
-   需建置 Active Directory 網域
    -   隸屬於DC的
-   Enterprise Admins
    -   DC1 才有, 網域的 Admin
-   獨立伺服器無法被授權
    -   但是可以架DHCP
    -   網域沒設定DC的話,就可以正常使用
    -   一旦裝了DC,有AD環境,獨立伺服器的DHCP server就無法啟動
