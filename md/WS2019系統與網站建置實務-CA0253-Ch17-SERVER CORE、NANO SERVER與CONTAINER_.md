# Server Core概觀

* 提供一個小型化的運作環境，可降低系統維護與管理需求、減少硬碟的使用量、減少被攻擊面
* 支援部分伺服器角色，例如：
  * Active Directory憑證服務、Active Directory網域服務、DHCP伺服器、DNS伺服器等等
* 不提供Windows視窗管理介面
  * 可執行Powershell\.exe

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_0.png)

# 變更電腦名稱

* netdom  renamecomputer  ServerCoreBase  /NewName:ServerCore1
  * 假設目前的電腦名稱為ServerCoreBase
  * hostname或ipconfig /all
* shutdown  /r  /t  0
* Sconfig

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_1.png)

# 變更IP位址 - 1

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_2.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_3.png)

__netsh__  __  interface  ipv4  set  address  name=“5”  source=__  __dhcp__

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_4.png)

__netsh__  __  interface ipv4 delete __  __dnsserver__  __  name=“5”  address=192\.168\.8\.1__

# 變更IP位址 - 2

執行Sconfig，然後選擇 __8__  __） 網路設定__

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_5.png)

# PowerShell指令

取的IP設定值：Get\-NetIPConfiguration

靜態設定IP位址、預設閘道：

New\-NetIPaddress \-InterfaceIndex 5 \-IPAddress 192\.168\.8\.41 \-PrefixLength 24

\-DefaultGateway 192\.168\.8\.254

改回動態設定IP位址：

Set\-NetIPInterface \-InterfaceIndex 5 \-Dhcp Enabled

將預設閘道192\.168\.8\.254清除：

Remove\-NetRoute \-Interfaceindex 5 \-NextHop 192\.168\.8\.254

指定DNS伺服器：

Set\-DnsClientServerAddress \-InterfaceIndex 5 \-ServerAddresses 192\.168\.8\.1

改回動態設定DNS伺服器：

Set\-DnsClientServerAddress \-InterfaceIndex 5 \-ResetServerAddresses

# 啟用 Server Core

slmgr\.vbs  –ipk   <25個字元的金鑰字串 _>_

slmgr\.vbs  –ato

cscript  \\Windows\\System32\\slmgr\.vbs  ServerCore1  <使用者名稱>   <密碼>   \-ato

# 加入網域

Netdom join ServerCore1  /domain:sayms\.local  /userD:administrator passwordD:111aaAA

Netdom join ServerCore1 /domain:sayms\.local /userD:administrator /passwordD:\*

shutdown  /r  /t  0

登入時：在登入畫面按2次Esc鍵選擇 __其他使用者__ 例如輸入sayms\.local\\administrator

Hyper\-V：點選 __檢視__ 功能表、取消勾選 __加強的工作階段__ ，按Esc鍵才會正常

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_6.png)

# 將網域使用者加入本機Administrators群組

* net  localgroup  administrators  /add  sayms\.local\\peter
  * 假設是網域sayms\.local使用者peter
* net  localgroup  administrators  /add  sayms\.local\\peter
* Sconfig
  * 選擇 __3__  __） 新增本機系統管理員__

# 變更日期與時間

* control  timedate\.cpl
* Sconfig
  * 選擇 __9__  __） 日期和__  __時間__
* 查看系統的版本
  * systeminfo\.exe
* 查看系統資訊
  * msinfo32\.exe

# 檢視所有角色與功能的狀態

* dism  /online  /get\-features  /format:table
* dism  /online  /get\-features  /format:table  >  t1\.txt
* PowerShell
  * Get\-WindowsFeature
  * Install\-WindowsFeature  <角色或功能名稱>
  * Uninstall\-WindowsFeature

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_7.png)

# DNS伺服器角色 - 1

* dism  /online  /enable\-feature  /featurename:DNS\-Server\-Full\-Role
* dism  /online  /disable\-feature  /featurename:DNS\-Server\-Full\-Role
* PowerShell
  * Install\-WindowsFeature  DNS  \-IncludeManagementTools
  * UnInstall\-WindowsFeature  DNS  \-IncludeManagementTools
* net  start  dns、net  stop  dns

# DNS伺服器角色 - 2

* dnscmd localhost /ZoneAdd saycore\.local /Primary /file saycore\.local\.dns
* Dnscmd ServerCore1 /recordadd saycore\.local Win10PC5 A  192\.168\.10\.5
* PowerShell
  * Add\-DnsServerPrimaryZone \-Name "saycore\.local" \-ZoneFile "saycore\.local\.dns“
  * Add\-DnsServerResourceRecordA \-Name "Win10PC5" \-ZoneName "saycore\.local"
  * \-IPv4Address "192\.168\.8\.5"

# DHCP伺服器角色 - 1

* dism  /online  /enable\-feature  /featurename:DHCPServer
* Install\-WindowsFeature  DHCP  \-IncludeManagementTools
* netsh  dhcp  add  server  ServerCore1\.sayms\.local  192\.168\.8\.41
* netsh  dhcp  show  server
* netsh  dhcp  delete  server  ServerCore1\.sayms\.local  192\.168\.8\.41
* Powershell
  * Add\-DhcpServerInDC  \-DNSName  ServerCore1\.sayms\.local
  * Get\-DhcpServerInDC
  * Remove\-DhcpServerInDC  \-DNSName  ServerCore1\.sayms\.local

* net  start  dhcpserver 、net  stop  dhcpserver
* sc  config  dhcpserver  start=auto
  * __auto__ （自動）也可以改為 __demand__ （手動）、 __disabled__ （停用）或 __delayed\-auto__ （自動 （延遲啟動））

# 檔案服務角色

Dism  /online  /enable\-feature  /featurename:File\-Services

Dism  /online  /enable\-feature  /featurename:FRS\-Infrastructure

Dism  /online  /enable\-feature  /featurename:DFSN\-Server

Dism  /online  /enable\-feature  /featurename:DFSR\-Infrastructure\-ServerEdition

Dism  /online  /enable\-feature  /all  /featurename:ServerForNFS\-Infrastructure

Dism  /online  /enable\-feature  /featurename:ServicesForNFS\-ServerAndClient

# Hyper-V角色

* Dism  /online  /enable\-feature  /featurename:Microsoft\-Hyper\-V
  * 可在其他電腦利用 __Hyper\-V__  __管理員__ 主控台來管理

# 列印服務角色

Dism /online  /enable\-feature  /all  /featurename:Printing\-Server\-Role

Dism /online /enable\-feature /featurename:Printing\-Server\-Foundation\-Features

Dism  /online  /enable\-feature  /featurename:Printing\-LPDPrintService

# Active Directory憑證服務與Active Directory網域服務

Dism /online /enable\-feature /featurename:CertificateServices

Dism /online /enable\-feature /all /featurename:DirectoryServices\-DomainController

# 網頁伺服器（IIS）角色

Dism  /online  /enable\-feature  /all  /featurename:IIS\-WebServer

Dism  /online  /enable\-feature  /featurename:IIS\-WebServerRole

Dism  /online  /enable\-feature  /featurename:IIS\-BasicAuthentication

dism  /online  /get\-features  /format:table  >  t1\.txt

# Server Core 應用程式相容性FOD

* 為了提高應用程式的相容性
  * 有些應用程式需要圖形介面的環境
* 讓系統管理員可以透過圖形介面來更容易的管理伺服器
* 提供部分圖形介面管理工具，例如：
  * 事件檢視器
  * 效能監視器
  * 資源監視器
  * 裝置管理員
  * 檔案總管
  * 磁碟管理
  * …

# 下載與安裝Server Core 應用程式相容性FOD

* https://www\.microsoft\.com/zh\-tw/evalcenter/evaluate\-windows\-server\-2019
* 掛載ISO檔案\(假設是被掛載在E:\)
  * Mount\-DiskImage  \-ImagePath  \\\\dc1\\tools\\ServerCoreFOD\.ISO
* 安裝
  * DISM /Online  /Add\-Capability  /CapabilityName:"ServerCore\.AppCompatibility
  * __       __ ~~~~0\.0\.1\.0"  /Source:E:  /LimitAccess

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_8.png)

# 使用圖形介面工具

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_9.png)

# 安裝瀏覽器Internet Explorer

Mount\-DiskImage  \-ImagePath  \\\\dc1\\tools\\ServerCoreFOD\.ISO

Dism /online /add\-package:E:“Microsoft\-Windows\-InternetExplorer\-

Optional\-Package ~31bf3856ad364e35~amd64~~\.cab“

重新啟動電腦後利用start iexplore\.exe指令來啟動Internet Explorer

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_10.png)

# 透過伺服器管理員來管理 Server Core - 1

* 利用SCONFIG來允許「伺服器管理員」遠端管理
* 也可以透過
  * __Configure\-SMRemoting\.exe __  __–enable__ 指令
  * __Configure\-SMRemoting\.exe  –disable__

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_11.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_12.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_13.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_14.png)

# 在Windows 10上透過「伺服器管理員」來遠端管理 - 1

* Windows 10
  * 1809之前的版本：請先到微軟網站下載與安裝 __Windows 10 __  __的遠端伺服器管理__  __工具__
  * 1809（含）之後的版本：點擊左下角 __開始__ 圖示點擊 __設定__ 圖示點擊 __應用程式__ 點擊 __選用功能__ 點擊 __新增功能__ 點擊 __RSAT__  __：伺服器管理員__ 按安裝鈕，
* 若未加入網域，還需執行以下PowerShell指令：
  * set\-item wsman:\\localhost\\Client\\TrustedHosts \-value  ServerCore1\.sayms\.local

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_15.png)

左下角 __開始__ 圖示伺服器管理員對著 __所有伺服器__ 按右鍵新增伺服器

若有加入網域的話，也可以透過 __Active Directory__ 標籤來搜尋

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_16.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_17.png)

若此Windows 10電腦未加入網域的話

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_18.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_19.png)

# 透過MMC管理主控台來管理 Server Core - 1

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_20.png)

* Server Core：開啟 __Windows __  __Defender __  __防火牆__ 的 __遠端事件記錄檔管理__ 規則
  * Enable\-NetFirewallRule  \-DisplayGroup  "遠端事件記錄檔管理“

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_21.png)

* 若來源電腦不是隸屬於AD DS網域
  * 先透過指令來指定用來連接 __ Server Core__ 的使用者帳戶，再透過MMC管理主控台來連接與管理 __ Server __  __Core__
* __ __ Cmdkey  /add:ServerCore1\.sayms\.local  /user:Administrator  /pass:111aaAA
* 也可在來源電腦上
  * 按Windows\+ R鍵輸入control後按Enter鍵點擊 __使用者帳戶__ （或 __使用者帳戶和家庭安全__ ）點擊 __管理__  __Windows__  __認證__ 點擊 __新增__  __Windows__  __認證__ 】來指定用來連接 __ Server Core__ 的使用者帳戶與密碼

# 透過遠端桌面來管理 Server Core - 1

在 __ Server Core__ 上啟用 __遠端__  __桌面__

__     __ cscript  C:\\Windows\\System32\\Scregedit\.wsf  /ar  0

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_22.png)

PowerShell指令

Set\-ItemProperty  \-Path 'HKLM:\\System\\CurrentControlSet\\Control

\\Terminal Server'  \-name  "fDenyTSConnections"  \-Value 0

Enable\-NetFirewallRule  \-DisplayGroup  "遠端桌面"

Set\-ItemProperty  \-Path 'HKLM:\\System\\CurrentControlSet\\Control

\\Terminal Server\\WinStations\\RDP\-Tcp'  \-name "UserAuthentication"  Value  1

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_23.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_24.png)

到來源電腦按Windows\+ R鍵輸入 __mstsc__ 按 __確定__ 鈕】（也可以透過[前面投影片](slide30.xml)中的 __遠端__  __桌面連線__ 選項

__logoff__ 指令以便結束此連線

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_25.jpg)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_26.png)

# 硬體裝置的安裝

* 隨插即用（PnP）功能會自動安裝驅動程式
* 手動安裝：pnputil  –i  –a   <驅動程式的inf檔>
* 查看已經安裝的驅動程式
  * sc  query  type=driver
  * sc  query  type=driver  >  t1\.txt
* 停用某個服務的話
  * sc  delete  <服務名稱>

# 容器與Docker

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_27.png)

* 不需要來賓作業系統，因此容器的體積小、載入執行快
* 容器內包含了執行此應用程式所需的所有元件，例如程式碼、函式庫、環境設定等
* 微軟支援兩種不同的容器
  * Windows Server Container
  * Hyper\-V Container
* 透過Docker來建立與管理容器

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_28.png)

# 安裝Docker

安裝Docker\-Microsoft PackageManagement Provider

Install\-Module \-Name DockerMsftProvider \-Repository PSGallery \-Force

安裝最新版的Docker

Install\-Package \-Name docker \-ProviderName DockerMsftProvider

重新啟動電腦：Restart\-Computer  \-Force

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_29.png)

# 查看所安裝的docker版本

Get\-Package \-Name Docker \-ProviderName DockerMsftProvider

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_30.png)

Find\-Package \-Name Docker \-ProviderName DockerMsftProviderProvider

Install\-Package \-Name Docker \-ProviderName DockerMsftProvider \-Update –Force

Start\-Service Docker

---

Windows 10：下載與安裝Docker Desktop for Windows、點選Switch to Windows containers…，

# 部署第一個容器

* 從Docker Hub下載映象檔、部署容器來執行映象檔裡的Hello World
  * 會先從本機硬碟來找尋是否有此映象檔
* docker run hello\-world

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_31.png)

# 查看現有的映象檔與容器

docker images

docker ps –a

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_32.png)

# 刪除容器、映象檔

docker rm < _容器識別碼_ >

docker rmi < _映象檔識別碼_ >

# 可先下載、再執行

docker pull hello\-world

docker run hello\-world

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_33.png)

# Windows基礎映象檔

* Nano Server
  * 最精巧的版本，內含\.NET core，適合雲端應用程式
* Windows Server Core
  * 適合傳統的應用程式、支援完整的\.NET framework
* Windows
  * 其映象檔最龐大，包含大部分的Windows元件，對Windows應用程式的支援最廣

# 基礎映象檔存放點與下載基礎映象檔

https://hub\.docker\.com/\_/microsoft\-windows\-nanoserver

https://hub\.docker\.com/\_/microsoft\-windows\-servercore

https://hub\.docker\.com/\_/microsoft\-windows

docker pull mcr\.microsoft\.com/windows/nanoserver:1809

docker pull mcr\.microsoft\.com/windows/servercore:1809

docker pull mcr\.microsoft\.com/windows:1809

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_34.png)

# 啟動Server Core容器

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_35.png)

啟動後執行 __命令提示字元__ cmd

docker run \-it mcr\.microsoft\.com/windows/servercore:1809 cmd

# 中斷連接容器測試

Ctrl 不放再按 P 、Q鍵 或 點擊視窗右上角的X

利用docker ps –a來查看容器的狀態

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_36.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_37.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_38.png)

# 重新連接容器

docker attach < _容器識別碼_ >

# 結束執行容器

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_39.png)

# 重新啟動此容器

* docker start < _容器識別碼_ >、 docker attach < _容器識別碼_ >
* 若重新執行docker run指令，則它會另外建立與執行一個新的容器
  * 容器在執行期間所做的變更，是被儲存在沙盒，不會變更到映象檔，因此另外執行的新容器，其環境仍然是與原映象檔相同
  * 可用docker ps –a來查看

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_40.png)

# 建立新的映象檔 - 1

利用現有容器的內容

利用docker commit指令

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_41.png)

# 建立新的映象檔 - 2

利用docker ps –a來查看 __容器識別碼__

透過docker commit指令，以便利用容器的內容來建立新的映象檔

透過docker images指令來查看新建立的映象檔

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_42.png)

# 驗證新映象檔

docker run \-it newdir

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_43.png)

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

利用記事本建立此檔案時，儲存檔案時在檔名前後加上雙引號"

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_44.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_45.png)

# 建立映象檔

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_46.png)

* docker build \-t myiis C:\\test
  * 假設dockerfile檔在C:\\test資料夾內

# 查看新建立的映象檔與執行容器

docker images

docker run –it myiis

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_47.png)

# 驗證執行結果

先在容器內利用ipconfig來查看其IP位址

接著到容器主機開啟Internet Explorer來測試

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch17-SERVER%20CORE%E3%80%81NANO%20SERVER%E8%88%87CONTAINER_48.png)

\* 關於Windows dockerfile的進一步相關說明，可參考以下的微軟網址：

https://docs\.microsoft\.com/zh\-tw/virtualization/windowscontainers/

manage\-docker/manage\-windows\-dockerfile

