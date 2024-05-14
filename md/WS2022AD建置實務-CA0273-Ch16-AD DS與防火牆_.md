第 16 章 AD DS與防火牆

16\-1 AD DS相關的連接埠

16\-2 限制動態RPC連接埠的使用範圍

16\-3 IPSec與VPN連接埠AD DS相關的連接埠

# 16-1 AD DS相關的連接埠

不同的網路服務會使用到不同的TCP或UDP連接埠（port），若防火牆沒有開放相關連接埠的話，將造成這些服務無法正常運作

| 服務 | TCP連接埠 | UDP連接埠 |
| :-: | :-: | :-: |
| RPC Endpoint Mapper | 135 |   |
| Kerberos | 88 | 88 |
| LDAP | 389 | 389 |
| LDAPS （LDAP over SSL） | 636 | 636 |
| LDAP GC （LDAP Global Catalog） | 3268 |   |
| LDAPS GC （LDAP Global Catalog over SSL） | 3269 |   |
| SMB （Microsoft CIFS） | 445 |   |
| DNS | 53 | 53 |
| Network Time Protocol （NTP） |   | 123 |
| AD DS資料庫複寫、檔案複寫服務（FRS）、分散式檔案系統（DFS）等服務 | 使用動態連接埠：需限制<br />連接埠範圍或變更為靜態連接埠 |  |
| NetBIOS Name Service |   | 137 |
| NetBIOS Datagram Service |   | 138 |
| NetBIOS Session Service | 139 |   |

# 將用戶端電腦加入網域、使用者登入時會用到的連接埠

Microsoft CIFS：445/TCP

Kerberos：88/TCP、88/UDP

DNS：53/TCP、53/UDP

LDAP：389/TCP、389/UDP

Netlogon服務： NetBIOS Name Service\(137/UDP\)/NetBIOS Datagram Service\(138/UDP\)/NetBIOS Session Service\(139/TCP\)與SMB\(445/ TCP\)

# 電腦登入時會用到的連接埠

Microsoft CIFS：445/TCP

Kerberos：88/TCP、88/UDP

LDAP：389/UDP

DNS：53/TCP、53/UDP

# 建立網域信任時會用到的連接埠

Microsoft CIFS：445/TCP

Kerberos：88/TCP、88/UDP

LDAP：389/TCP、389/UDP

LDAPS：636/TCP\(若使用SSL的話\)

DNS：53/TCP、53/UDP

# 驗證網域信任時會用到的連接埠

Microsoft CIFS：445/TCP

Kerberos：88/TCP、88/UDP

LDAP：389/TCP、389/UDP

LDAPS：636/TCP（若使用SSL的話）

DNS：53/TCP、53/UDP

Netlogon服務：NetBIOS Name Service\(137/UDP\)/NetBIOS Datagram Service\(138/UDP\)/NetBIOS Session Service\(139/TCP\)與SMB\(445/ TCP\)

# 存取檔案資源時會用到的連接埠

SMB（445/TCP）或

NetBIOS Name Service\(137/UDP\)/NetBIOS Datagram Service \(138/UDP\)/NetBIOS Session Service\(139/TCP\)與SMB\(445/ TCP\)

# 執行DNS查詢時會用到的連接埠

# 執行AD DS資料庫複寫時會用到的連接埠

* AD DS資料庫複寫
  * 使用動態RPC連接埠（49152－65535）。動態RPC連接埠可以被限制在一段較小的範圍內，因此我們只要在防火牆開放這一小段範圍的TCP連接埠即可。您也可以自行指定一個靜態的連接埠
* RPC Endpoint Mapper：135/TCP
  * 使用動態RPC連接埠時，需要搭配RPC Endpoint Mapper服務，因此請在防火牆開放此服務的連接埠
* Kerberos：88/TCP、88/UDP
* LDAP：389/TCP、389/UDP
* LDAPS：636/TCP（若使用SSL的話）
* DNS：53/TCP、53/UDP
* Microsoft CIFS：445/TCP

# 檔案複寫服務（FRS）會用到的連接埠

* 網域功能等級Windows Server 2008以下的話
  * 使用FRS複寫SYSVOL資料夾。採用動態RPC連接埠，因此若將動態RPC連接埠限制在一段較小範圍內的話，則只要在開放此範圍的連接埠即可
  * 需要搭配RPC Endpoint Mapper服務，因此需開放RPC Endpoint Mapper：135/TCP。
  * 也可以自行指定一個靜態的連接埠

# 分散式檔案系統（DFS）會用到的連接埠

* 網域功能等級為Windows Server 2008（含）以上的話，網域控制站之間在複寫SYSVOL資料夾時需利用 __DFS__  __複寫服務__
  * LDAP：389/TCP、389/UDP
  * Microsoft CIFS：445/TCP
  * NetBIOS Datagram Service：138/UDP
  * NetBIOS Session Service：139/TCP
  * Distributed File System（DFS）
    * 採用動態RPC連接埠，因此若將動態RPC連接埠限制在一段較小範圍內的話，則只要在開放此範圍的連接埠即可。也可以自行指定一個靜態的連接埠
  * RPC Endpoint Mapper：135/TCP
    * 使用動態RPC連接埠時，需要搭配RPC Endpoint Mapper服務

# 其他可能需要開放的連接埠

* LDAP GC 、LDAPS GC：3268/TCP、3269/TCP（若使用SSL的話）
  * 使用者登入時，網域控制站需要透過防火牆來向 __通用類別目錄伺服器__ 查詢使用者所隸屬的萬用群組資料的話
  * Microsoft Exchange Server需要存取 __通用類別目錄伺服器__ 的話
* Network Time Protocol（NTP）:123/UDP
  * 時間同步，參見第10章
* NetBIOS的相關服務：137/UDP、138/UDP、139/TCP
  * 支援舊用戶端來登入、瀏覽網路上的芳鄰

# 16-2 限制動態RPC連接埠的使用範圍

* 以Outlook\(MAPI用戶端\)與Exchange Server之間為例
  * Outlook先連接Exchange Server的RPC Endpoint Mapper\(RPC Locator Services，TCP 135\)、RPC Endpoint Mapper再將Exchange Server所使用的連接埠\(49152 – 65535\)通知用戶端、用戶端Outlook再透過此連接埠來連接Exchange Server
* AD DS資料庫的複寫、Outlook與 Exchange Server之間的溝通、FRS檔案複寫服務、DFS分散式檔案系統等預設都是使用動態RPC連接埠
* 動態RPC連接埠可以被限制在一段較小的範圍內，因此我們只要在防火牆開放這段範圍的連接埠即可

# 限制所有服務的動態RPC連接埠範圍

假設IPv4或IPv6都要限制在從8000起開始，總共1000個連接埠號碼

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch16-AD%20DS%E8%88%87%E9%98%B2%E7%81%AB%E7%89%86_0.png)

* 也可以執行以下的netsh指令
  * __netsh__  __  __  __int__  __  ipv4  set  __  __dynamicport__  __  __  __tcp__  __  start=8000  __  __num__  __=1000__
  * __netsh__  __  __  __int__  __  ipv4  set  __  __dynamicport__  __  __  __udp__  __  start=8000  __  __num__  __=1000__
  * __netsh__  __  __  __int__  __  ipv6  set  __  __dynamicport__  __  __  __tcp__  __  start=8000  __  __num__  __=1000__
  * __netsh__  __  __  __int__  __  ipv6  set  __  __dynamicport__  __  __  __udp__  __  start=8000 __  __num__  __=1000__

# 檢查目前的動態RPC連接埠範圍 - 1

* Get\-NetTCPSetting \-Setting Internet
  * Get\-NetTCPSetting
* Get\-NetUDPSetting

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch16-AD%20DS%E8%88%87%E9%98%B2%E7%81%AB%E7%89%86_1.png)

* 也可以執行以下的netsh指令
  * __netsh__  __  __  __int__  __  ipv4  show  __  __dynamicport__  __  __  __tcp__
  * __netsh__  __  __  __int__  __  ipv4  show  __  __dynamicport__  __  __  __udp__
  * __netsh__  __  __  __int__  __  ipv6  show  __  __dynamicport__  __  __  __tcp__
  * __netsh__  __  __  __int__  __  ipv6  show  __  __dynamicport__  __  __  __udp__

# 檢視目前所使用的連接埠

* 開啟Windows PowerShell視窗、執行 __netstat__  __ \-n__ 指令
  * 也可以利用 __Get\-__  __NetTCPConnection__ 指令來查看

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch16-AD%20DS%E8%88%87%E9%98%B2%E7%81%AB%E7%89%86_2.png)

# 限制AD DS資料庫複寫使用指定的靜態連接埠

執行登錄編輯程式REGEDIT\.EXE

__HKEY\_LOCAL\_MACHINE\\SYSTEM\\__  __CurrentControlSet__  __\\Services\\NTDS\\Parameters__

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch16-AD%20DS%E8%88%87%E9%98%B2%E7%81%AB%E7%89%86_3.png)

---

完成後重新開機

# 檢視AD DS資料庫複寫時所使用到的連接埠

完成登錄值修改後重新開機

先利用 __Active Directory__  __站台及服務__ 來手動與其他網域控制站之間執行AD DS資料庫複寫

執行 __netstat__  __ \-n__ 指令

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch16-AD%20DS%E8%88%87%E9%98%B2%E7%81%AB%E7%89%86_4.png)

# 限制FRS使用指定的靜態連接埠

* 網域功能等級是Windows Server 2008以下的話，則網域控制站之間在複寫SYSVOL資料夾時，會使用FRS\(預設是使用動態RPC連接埠\)
* 可以指定一個靜態的連接埠
  * 登錄編輯程式REGEDIT\.EXE
  * RPC TCP/IP Port Assignment，其資料型態是REG\_DWORD
* __HKEY\_LOCAL\_MACHINE\\SYSTEM\\__  __CurrentControlSet__  __\\Services\\NTFRS\\Parameters__

* 網域功能等級為Windows Server 2008（含）以上的話，則網域控制站之間在複寫SYSVOL資料夾時需利用 __DFS__  __複寫服務__ \(預設是使用動態RPC連接埠\)
* 可以指定一個靜態的連接埠
  * Set\-DfsrServiceConfiguration  \-RPCPort  34567
    * 也可以執行 __DFSRDIAG  __  __StaticRPC__  __  /Port:34567__
  * 完成後重新啟動此網域控制站

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch16-AD%20DS%E8%88%87%E9%98%B2%E7%81%AB%E7%89%86_5.png)

# 16-3 IPSec與VPN連接埠

* IPSec所使用的通訊協定與連接埠
  * Encapsulation Security Payload （ESP）：通訊協定號碼為50
  * Authentication Header （AH）：通訊協定號碼為51
  * Internet Key Exchange （IKE）：所使用的是UDP連接埠號碼500
* PPTP VPN所使用的通訊協定與連接埠
  * General Routing Encapsulation （GRE）：通訊協定號碼為47
  * PPTP：所使用的是TCP連接埠號碼1723
* L2TP/IPSec所使用的通訊協定與連接埠
  * Encapsulation Security Payload （ESP）：通訊協定號碼為50
  * Internet Key Exchange （IKE）：所使用的是UDP連接埠號碼500
  * NAT\-T：所使用的是UDP連接埠號碼4500，它讓IPSec可以穿越NAT

---

雖然L2TP/IPSec還會使用到UDP 連接埠1701，但它是被封裝在IPSec封包內，因此不需要在防火牆開放此連接埠

