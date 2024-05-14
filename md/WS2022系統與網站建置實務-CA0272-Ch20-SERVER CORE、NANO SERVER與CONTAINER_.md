第 20 章 Server Core、Nano Server與Container

20\-1 Server Core概觀

20\-2 Server Core的基本設定

20\-3 在 Server Core內安裝角色與功能

20\-4 Server Core 應用程式相容性FOD

20\-5 遠端管理 Server Core

20\-6 容器與Docker

# Server Core概觀

* 提供一個小型化的運作環境，可降低系統維護與管理需求、減少硬碟的使用量、減少被攻擊面
* 支援部分伺服器角色，例如：
  * Active Directory憑證服務、Active Directory網域服務、DHCP伺服器、DNS伺服器等等

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_0.png)

# 伺服器設定工具程式

* Sconfig
  * 透過它來更容易的執行一些基本管理工作
    * 例如透過選項2來變更電腦名稱、透過選項8來變更IP位址等設定、然後透過選項1來將此電腦加入網域
  * __Set\-__  __SConfig__  __  \-__  __AutoLaunch__  __  $__  __False__
  * __Set\-__  __SConfig__  __  \-__  __AutoLaunch__  __  $__  __True__
* 登出：選項12或執行 __Logoff__

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_1.png)

# 改用其他帳戶登入

登入畫面上按2次Esc鍵、選擇 __其他__  __使用者__

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_2.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_3.png)

# 變更電腦名稱與IP設定值

* 變更電腦名稱
* Rename\-Computer \-ComputerName  ServerCoreBase  \-NewName ServerCore1  –Restart
* 取的IP設定值：Get\-NetIPConfiguration
* 靜態設定IP位址、預設閘道：
  * New\-NetIPAddress  \-InterfaceIndex  5  \-IPAddress  192\.168\.8\.41          \-PrefixLength  24 \-DefaultGateway 192\.168\.8\.254
* 改回動態設定IP位址：
* Set\-NetIPInterface  \-InterfaceIndex  5  \-Dhcp  Enabled
* 將預設閘道192\.168\.8\.254清除：
* Remove\-NetRoute  \-Interfaceindex  5  \-NextHop  192\.168\.8\.254
* 指定DNS伺服器：
* Set\-DnsClientServerAddress  \-InterfaceIndex  5  \-ServerAddresses  192\.168\.8\.1
* 改回動態設定DNS伺服器：
* Set\-DnsClientServerAddress  \-InterfaceIndex  5  \-ResetServerAddresses

---

若要修改IP設定值的話，請用Set-NetIPAddress指令

# 啟用 Server Core

* 輸入產品金鑰
  * __slmgr\.vbs  –__  __ipk__  __   <25__  __個字元的金鑰字串__  _>_
* 啟用 __ Server __  __Core__
  * __slmgr\.vbs  –__  __ato__

# 加入網域

* Add\-Computer \-DomainName  sayms\.local –Restart
  * 輸入有權利將電腦加入網域的使用者帳戶與密碼
* Add\-Computer  \-DomainName  sayms\.local \-Credential  sayms\\Administrator  \-Restart
* 脫離網域
  * Add\-Computer  \-WorkgroupName  TestGroup  \-Restart

# 新增本機使用者與群組帳戶

* 新增本機使用者
  * $Password = Read\-Host  \-AsSecureString
  * New\-LocalUser  \-Name Jackie  \-Password  $Password  \-FullName  “Jack Wang”
* 查看目前有哪一些使用者帳戶
  * Get\-LocalUser
* 刪除使用者
  * Remove\-LocalUser  \-Name  Jackie
* 新增群組
  * New\-LocalGroup  \-Name  SALES
* 刪除群組
  * Remove\-LocalGroup  \-Name  SALES

---

若要在Active Directory資料庫內新增使用者與群組帳戶的話，請用New-ADUser與New-ADGroup指令

# 將網域使用者加入本機Administrators群組

* 將使用者加入本機Administrators群組
  * Add\-LocalGroupMember  \-Group Administrators  \-Member  sayms\.local\\peter
* 檢查Administrators群組的成員
  * Get\-LocalGroupMember  \-Group  Administrators
* Sconfig
  * 選擇 __3__  __） 新增本機系統管理員__

# 檢視所有角色與功能的狀態

Get\-WindowsFeature

Install\-WindowsFeature  <角色或功能名稱>

Uninstall\-WindowsFeature

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_4.png)

# 安裝DNS伺服器角色 - 1

* 安裝DNS伺服器角色（包含管理DNS伺服器的工具）
  * Install\-WindowsFeature  DNS  \-IncludeManagementTools
  * Get\-WindowsFeature

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_5.png)

# DNS伺服器角色 - 2

* 建立主要正向對應區域saycore\.local
  * Add\-DnsServerPrimaryZone \-Name saycore\.local \-ZoneFile  saycore\.local\.dns
* 新增記錄
  * Add\-DnsServerResourceRecordA \-Name Win11PC5 \-ZoneName saycore\.local
  * \-IPv4Address 192\.168\.8\.5
* 檢視saycore\.local區域內的記錄
  * Get\-DnsServerResourceRecord  \-ZoneName  saycore\.local

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_6.png)

# DNS伺服器角色 - 3

* 停止、啟動或重新啟動DNS伺服器
  * Stop\-Service  DNS、Start\-Service  DNS或Restart\-Service  DNS
* 移除DNS伺服器角色
  * UnInstall\-WindowsFeature  DNS  \-IncludeManagementTools
* 查看與DNS伺服器有關的功能
  * Get\-WindowsFeature \*DNS\*
* 若要安裝RSAT\-DNS\-Server
  * Install\-WindowsFeature  RSAT\-DNS\-Server

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_7.png)

# DHCP伺服器角色

* 安裝DHCP伺服器角色
  * Install\-WindowsFeature  DHCP  \-IncludeManagementTools
* 在AD DS網域環境中授權
  * Add\-DhcpServerInDC \-DNSName ServerCore1\.sayms\.local  \-IPAddress 192\.168\.8\.41
* 檢查安裝的結果
  * Get\-DhcpServerInDC
* 解除授權
  * Remove\-DhcpServerInDC  \-DNSName  ServerCore1\.sayms\.local  \-IPAddress  192\.168\.8\.41
* 停止、啟動或重新啟動DNS伺服器
  * Stop\-Service  DHCPServer、Start\-Service  DHCPServer或Restart\-Service  DHCPServer

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_8.png)

# 安裝其他常見的角色 - 1

* 安裝Hyper\-V角色
  * Install\-WindowsFeature  Hyper\-V
* 安裝Active Directory網域服務（AD DS）
  * Install\-WindowsFeature  AD\-Domain\-Services  \-IncludeManagementTools
  * Install\-ADDSForest  \-DomainName  sayms2\.local

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_9.png)

* 安裝Active Directory憑證服務（AD CS）
  * Get\-WindowsFeature  \*AD\*
  * Install\-WindowsFeature  AD\-Certificate\,ADCS\-Cert\-Authority\,ADCS\-Web\-Enrollment
* 設定為獨立根CA
  * Install\-AdcsCertificationAuthority  \-CAType  StandaloneRootCa –CACommonName  SaymsTestCA

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_10.png)

* 安裝網頁伺服器（IIS）
  * Install\-WindowsFeature  \-Name  Web\-Server  \-IncludeManagementTools
  * Get\-WindowsFeature  \*Web\*
  * Install\-WindowsFeature  \-Name  Web\-Server\,Web\-Basic\-Auth  \-IncludeManagementTools

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_11.png)

# Server Core 應用程式相容性FOD

* 有些應用程式需要圖形介面的環境
* 讓系統管理員可以透過圖形介面來更容易的管理伺服器
* 提供部分圖形介面管理工具，例如：
  * Microsoft管理主控台
  * 事件檢視器
  * 效能監視器
  * 資源監視器
  * 裝置管理員
  * 檔案總管
  * Windows PowerShell
  * 磁碟管理
  * …

# 下載與安裝Server Core 應用程式相容性FOD - 1

* 在Server Core電腦直接下載與安裝 __語言和選用功能__ ISO檔
  * 舊版稱為 __Server Core __  __應用程式相容性__  __FOD __ ISO檔
  * Add\-WindowsCapability  \-Online  \-Name  ServerCore\.AppCompatibility~~~~0\.0\.1\.0
  * Restart\-Computer

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_12.png)

* 若Server Core電腦無法連上Windows Update網站
  * 利用別台電腦來下載 __語言和選用功能__ ISO檔
  * https://www\.microsoft\.com/zh\-tw/evalcenter/evaluate\-windows\-server\-2022
* 在Server Core電腦上掛載ISO檔案\(假設檔案名稱是ServerCoreFOD\.ISO，且是被掛載在E:\)
  * Mount\-DiskImage  \-ImagePath  \\\\dc1\\tools\\ServerCoreFOD\.ISO
* 在Server Core電腦上安裝
  * Add\-WindowsCapability \-Online  \-Name ServerCore\.AppCompatibility~~~~0\.0\.1\.0
  * \-Source  E:\\LanguagesAndOptionalFeatures\\  \-LimitAccess
  * Restart\-Computer

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_13.png)

# 安裝瀏覽器Internet Explorer - 1

* Mount\-DiskImage  \-ImagePath  \\\\dc1\\tools\\ServerCoreFOD\.ISO
  * 假設被掛載在E:
* $package\_path="E:\\LanguagesAndOptionalFeatures\\Microsoft\-Windows\-
* InternetExplorer\-Optional\-Package~31bf3856ad364e35~amd64~~\.cab"
* Add\-WindowsPackage  \-Online  \-PackagePath  $package\_path

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_14.png)

重新啟動電腦後利用start iexplore\.exe指令來啟動Internet Explorer

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_15.png)

# 透過伺服器管理員來管理 Server Core - 1

* 利用SCONFIG來允許「伺服器管理員」遠端管理\(預設已允許\)
* 也可以透過以下指令
  * __Configure\-SMRemoting\.exe –__  __enable__
  * __Configure\-SMRemoting\.exe  –disable__

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_16.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_17.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_18.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_19.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_20.png)

# 在Windows 11上透過「伺服器管理員」來遠端管理 - 1

* 點擊下方的 __開始__ 圖示點擊 __設定__ 圖示點擊 __應用程式__ 處的 __選用功能__ 點擊 __新增選用功能__ 處的 __檢視功能__ 勾選 __RSAT__  __：伺服器管理員__ 按 __下一步__ 鈕按 __安裝__ 鈕
* 若未加入網域，則還需執行以下PowerShell指令：
  * set\-item  wsman:\\localhost\\Client\\TrustedHosts  \-value  ServerCore1

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_21.png)

點擊下方 __開始__ 圖示伺服器管理員對著 __所有伺服器__ 按右鍵新增伺服器

若有加入網域的話，也可以透過 __Active Directory__ 標籤來搜尋

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_22.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_23.png)

若此Windows 11電腦未加入網域的話

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_24.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_25.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_26.png)

# 透過MMC管理主控台來管理 Server Core - 1

* Server Core：開啟 __Windows Defender __  __防火牆__ 的 __遠端事件記錄檔管理__ 規則
  * Enable\-NetFirewallRule  \-DisplayGroup  “遠端事件記錄檔管理”

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_27.png)

* 來源電腦：對著下方的 __開始__ 圖示按右鍵電腦管理

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_28.png)

---

若要停用此規則的話，請將指令中的Enable改為Disable
在寫此書時，Server Core內無法輸入中文，暫時解決方法為執行以下指令Get-NetFirewallRule –DisplayGroup *，然後從畫面來找尋遠端事件記錄檔管理中文字，再將其複製(選取文字後按Enter鍵)、貼到Enable-NetFirewallRule指令行中

* 若來源電腦不是隸屬於AD DS網域
  * 先透過指令來指定用來連接 __ Server Core__ 的使用者帳戶，再透過MMC管理主控台來連接與管理 __ Server Core__
* __ __ Cmdkey  /add:ServerCore1\.sayms\.local  /user:Administrator  /pass:111aaAA
* 也可在來源電腦上
  * 按Windows\+ R鍵輸入control後按Enter鍵點擊 __使用者帳戶__ （或 __使用者帳戶和家庭安全__ ）點擊 __管理__  __Windows__  __認證__ 點擊 __新增__  __Windows__  __認證__ 】來指定用來連接 __ Server Core__ 的使用者帳戶與密碼

---

在圖中另一台電腦處所輸入的名稱，必須與在Cmdkey指令中（或控制台）所輸入的名稱相同。例如前面的範例指令Cmdkey中是輸入ServerCore1.sayms.local，則在圖中另一台電腦處，就必須輸入ServerCore1.sayms.local，不可以輸入ServerCore1或IP位址

# 透過遠端桌面來管理 Server Core - 1

在 __ Server Core__ 上啟用 __遠端__  __桌面__

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_29.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_30.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_31.png)

到來源電腦按Windows\+ R鍵輸入 __mstsc__ 按 __確定__ 鈕

__logoff__ 指令以便結束此連線

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_32.jpg)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_33.png)

# 容器與Docker

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_34.png)

* 不需要來賓作業系統，因此容器的體積小、載入執行快
* 容器內包含了執行此應用程式所需的所有元件，例如程式碼、函式庫、環境設定等
* 微軟支援兩種不同的容器
  * Windows Server Container
  * Hyper\-V Container
* 透過Docker來建立與管理容器

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_35.png)

---

微軟支援兩種不同的容器：Windows Server Container與Hyper-V Container。Windows Server Container類似前面圖 20-6‑2的架構，它們共用主機作業系統核心，體積較輕巧、運作較快；Hyper-V Container則是在擁有獨立Windows核心的隔離環境下運作，體積較大、運作較慢，但較安全。Windows Server 2022支援上述2種容器、Windows 11（專業/企業版）僅支援Hyper-V容器

# 安裝Docker

安裝Docker\-Microsoft PackageManagement Provider

Install\-Module \-Name DockerMsftProvider \-Repository PSGallery \-Force

安裝最新版的Docker

Install\-Package \-Name docker \-ProviderName DockerMsftProvider

重新啟動電腦：Restart\-Computer

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_36.png)

# 查看所安裝的docker版本 - 1

docker version與docker info

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_37.png)

---

Windows 11：下載與安裝Docker Desktop for Windows、點選Switch to Windows containers…，

* Get\-Package \-Name Docker \-ProviderName DockerMsftProvider
* 找尋是否有新版本docker可供安裝
  * Find\-Package \-Name Docker \-ProviderName DockerMsftProviderProvider
  * Install\-Package \-Name Docker \-ProviderName DockerMsftProvider \-Update –Force
  * Start\-Service Docker

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_38.png)

---

1.為了確保容器主機的Windows作業系統是最新的，因此請先上網更新系統
2. Windows 11：下載與安裝Docker Desktop for Windows、點選Switch to Windows containers…，

# 部署第一個容器

* 從Docker Hub下載映象檔、部署容器來執行映象檔裡的Hello World
  * docker run hello\-world
  * 會先從本機硬碟來找尋是否有此映象檔，若有的話，則直接使用此映像檔，若無的話，會改從Docker Hub下載

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_39.png)

# 查看現有的映象檔與容器

docker images

docker ps –a

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_40.png)

# 刪除容器、映象檔

* docker rm < _容器識別碼_ >
  * docker  rm  546f9de95ff1
* docker rmi < _映象檔識別碼_ >
  * docker  rmi  d9974df6f614
  * 被容器使用中的映像檔無法刪除，需先刪除容器，再來刪除映像檔

# 可先下載映像檔後再執行

docker pull hello\-world

docker run hello\-world

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_41.png)

---

圖中假設已經將之前練習的容器與映像檔都刪除了，請先用docker images與docker ps -a來確認已經刪除

# Windows基礎映象檔

* __基底映像檔__ 替容器提供了作業系統環境
* Nano Server
  * 最精巧的版本，適合\.NET core應用程式。未含PowerShell
* Windows Server Core
  * 適合\.NET framework應用程式。適合需要遷移到不同環境執行的應用程式
* Windows
  * 有完整的API支援，對應用程式的支援度比Windows Server Core更高。映像檔最龐大
* Windows Server
  * 也有完整的API支援。支援GPU加速、IIS連線數沒有限制等。映像檔比Windows稍小

# 基礎映象檔存放點與下載基礎映象檔

https://hub\.docker\.com/\_/microsoft\-windows\-nanoserver

https://hub\.docker\.com/\_/microsoft\-windows\-servercore

https://hub\.docker\.com/\_/microsoft\-windows

https://hub\.docker\.com/\_/microsoft\-windows\-server

docker pull mcr\.microsoft\.com/windows/nanoserver:ltsc2022

docker pull mcr\.microsoft\.com/windows/servercore:ltsc2022

docker pull mcr\.microsoft\.com/windows:20H2

docker pull mcr\.microsoft\.com/windows/server:ltsc2022

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_42.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_43.png)

# 啟動Server Core容器

* docker run \-it mcr\.microsoft\.com/windows/servercore:ltsc2022 cmd
  * 啟動後執行cmd（ __命令提示字元__ ）
  * \-it選項：避免“Server Core啟動後立刻結束執行、並跳回容器主機的PowerShell環境\(不停留在 __命令提示__  __字元__ \)”
  * __Cmd__ 可改為 __PowerShell：__ 利用PowerShell指令來管理容器內的Windows系統
  * 執行了dir指令

# 中斷連接容器測試

* 點擊Cmd視窗右上角的X或按Ctrl 不放再按 P 、Q鍵
* 回PowerShell後，利用docker ps –a來查看容器的狀態
  * STATUS欄位的Up 12 minutes表示已經持續執行12分鐘了

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_44.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_45.png)

# 重新連接容器

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_46.png)

* docker attach < _容器識別碼_ >
  * 連接完成後會回到 __命令提示字元__ 內剛才執行dir的畫面

# 結束執行容器

* 在容器的 __命令提示字元__ 下執行exit指令
  * STATUS欄位的Exited字樣

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_47.png)

# 重新啟動此容器

* docker start < _容器識別碼_ >、 docker attach < _容器識別碼_ >
* 若重新執行docker run指令，則它會另外建立與執行一個新的容器
  * 容器在執行期間所做的變更，是被儲存在沙盒，不會變更到映象檔，因此另外執行的新容器，其環境仍然是與原映象檔相同
  * 可用docker ps –a來查看

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_48.png)

# 建立新的映象檔 - 1

* 利用現有容器的內容
  * 使用docker commit指令
  * 先連接到前面練習的此容器，建立一個名稱為testdir的資料夾

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_49.png)

# 建立新的映象檔 - 2

* 利用docker ps –a來查看 __容器識別碼__
* 透過docker commit指令，以便利用容器的內容來建立新的映象檔
  * 此映象檔內會有C:\\testdir資料夾
* 透過docker images指令來查看新建立的映象檔

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_50.png)

# 驗證新映象檔

* 使用docker run指令來建立與執行內含此新映像檔的容器
  * docker run \-it newdir

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_51.png)

# 複製檔案到docker容器 - 1

* 假設要將容器主機C:\\testfile\.txt複製到前面容器的C:\\testdir資料夾內
* 按著Ctrl 不放，再按 P 、Q鍵來跳回容器主機的PowerShell視窗
* 利用docker ps –a查看容器 __容器識別碼__ ，並確認容器在執行中
* 透過以下指令來複製檔案
  * docker cp  C:\\testfile\.txt  824d079b14c7:C:\\testdir

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_52.png)

* 透過docker attach來連接此容器
  * 透過dir  testdir指令來確認檔案已複製成功
* 將容器內的檔案、資料夾複製到容器主機，例如：
  * docker  cp  824d079b14c7:C:\\testdir  C:\\

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_53.png)

# 自訂映象檔

* 可採用
  * docker commit指令
  * dockerfile檔與docker build指令
* dockerfile是文字檔，主要包含：
  * 利用FROM指令來指定需要使用的基礎映象檔（base image）
  * 利用LABEL指令標註此映象檔的維護者
  * 利用RUN指令來指定在映象檔建立過程中需執行的指令
  * 利用CMD指令來指定在部署內含此映象檔的容器時，需要執行的指令

# dockerfile範例

利用記事本建立此檔案時，儲存檔案時在檔名前後加上雙引號“”

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_54.png)

# 建立映象檔

* docker build \-t myiis C:\\test
  * 假設dockerfile檔在C:\\test資料夾內

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_55.png)

# 查看新建立的映象檔與執行容器

docker images

docker run –it myiis

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_56.png)

# 驗證執行結果

先在容器內利用ipconfig來查看其IP位址

接著到容器主機開啟Internet Explorer來測試

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_57.png)

\* 關於Windows dockerfile的進一步相關說明，可參考以下的微軟網址：

https://docs\.microsoft\.com/zh\-tw/virtualization/windowscontainers/

manage\-docker/manage\-windows\-dockerfile

# 利用Windows Admin Center管理容器與映像 - 1

* 到微軟網站下載Windows Admin Center、安裝到容器主機上
* 到另一台Windows電腦上開啟瀏覽器Microsoft Edge
  * 輸入 __https://__  _容器主機電腦名稱_ /輸入系統管理員帳戶與密碼從 __名稱__ 處點擊容器主機的電腦名稱點擊右上角的 __設定__ 圖示點擊左邊 __閘道__ 處的 __延伸__  __模組__

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_58.png)

安裝完成後點選上方 __設定__ 處的 __伺服器__  __管理員__

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_59.png)

回到 __伺服器連線__ 畫面時點擊容器主機的電腦名稱

如圖所示來管理容器與映像（若 __工具__ 被摺疊起來的話，請先展開）

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch20-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_60.png)

