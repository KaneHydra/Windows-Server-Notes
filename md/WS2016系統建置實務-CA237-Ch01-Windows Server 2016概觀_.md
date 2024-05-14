第 1 章

Windows Server 2016概觀

1\-1  Windows Server 2016版本

1\-2  Windows網路架構

1\-3  TCP/IP通訊協定簡介

# 1-1	Windows Server 2016版本

Datacenter Edition： 適用於高度虛擬化和軟體定義資料中心環境

Standard Edition： 適用於低密度或非虛擬化的環境

Essentials Edition：適用於最多 25 名使用者，最多 50 部裝置的小型企業

| 版本 | Datacenter | Standard |
| :-: | :-: | :-: |
| Windows Server 核心功能 |  |  |
| 作業系統環境（OSE／Hyper-V 容器）數量 | 無限制 | 2 |
| Windows Server 容器 | 無限制 | 無限制 |
| Host Guardian Service |  |  |
| Nano Server |  |  |
| Storage Spaces Direct、Storage Replica 等儲存功能 |  |   |
| Shielded Virtual Machines |  |   |
| 網路堆疊 |  |   |

# 1-2	Windows網路架構

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch01-Windows%20Server%202016%E6%A6%82%E8%A7%80_0.tiff)

本機安全帳戶資料庫（SAM資料庫）

不一定需要伺服器等級的電腦

適合電腦數量不多的網路來採用（例如10或20台電腦）

# 網域架構的網路

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch01-Windows%20Server%202016%E6%A6%82%E8%A7%80_1.tiff)

Active Directory資料庫（AD DS資料庫）內包含著整個網域內所有使用者帳戶等相關資料

由Active Directory網域服務（AD DS）負責資料庫的新增、刪除、修改與查詢等工作

# 網域中的電腦種類

* 網域控制站
  * 需伺服器等級的電腦，例如Windows Server 2016 Datacenter、 Windows Server 2012 R2 Datacenter等
* 成員伺服器
  * Windows Server 2016 Datacenter/Standard/Essentials
  * Windows Server 2012\(R2\) Datacenter/Standard
  * Windows Server 2008\(R2\) Datacenter/Enterprise/Standard
* 其他目前較常用的Windows電腦
  * Windows 10 Enterprise/Pro/Education
  * Windows 8\.1\(8\) Enterprise/Pro
  * Windows 7 Ultimate/Enterprise/Professional
  * Windows Vista Ultimate/Enterprise/Business

# 1-3  TCP/IP通訊協定簡介

* 是目前最完整、最被廣泛支援的通訊協定
* 在TCP/IP網路上，每一台連接在網路上的電腦被稱為是一台 __主機__
* IP位址，例如192\.168\.1\.31
  * 網路辨識碼：192\.168\.1\.0
  * 主機辨識碼：31
* IP 等級

| Class | 網路<br />識別碼 | 主機<br />識別碼 | W 值可為 | 可支援的<br />網路數量 | 每個網路可支援的主機數量 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| A | W | X.Y.Z | 1-126 | 126 | 16,777,214 |
| B | W.X | Y.Z | 128-191 | 16,384 | 65,534 |
| C | W.X.Y | Z | 192-223 | 2,097,152 | 254 |

\* 127\.0\.0\.1供 __迴路測試__ 、也代表主機本身； 第1個IP位址代表網路本身、最後1   個代表廣播位址

# TCP/IP網路範例

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch01-Windows%20Server%202016%E6%A6%82%E8%A7%80_2.png)

# 子網路遮罩

| Class | 預設子網路遮罩(2進位) | 預設子網路遮罩(10進位) |
| :-: | :-: | :-: |
| A | 11111111  00000000  00000000  00000000 | 255.0.0.0 |
| B | 11111111  11111111  00000000  00000000 | 255.255.0.0 |
| C | 11111111  11111111  11111111  00000000 | 255.255.255.0 |

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch01-Windows%20Server%202016%E6%A6%82%E8%A7%80_3.png)

* CIDR（Classless Inter\-Domain Routing）無等級的劃分方式
  * 192\.168\.1\.0/24
  * 10\.120\.0\.0/16

# 預設閘道

一般主機若要與其他網路的主機溝通的話，需要透過路由器來轉送

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch01-Windows%20Server%202016%E6%A6%82%E8%A7%80_4.png)

# 私人IP的使用

| 網路識別碼 | 子網路遮罩 | IP位址範圍 |
| :-: | :-: | :-: |
| 10.0.0.0 | 255.0.0.0 | 10.0.0.1 - 10.255.255.254 |
| 172.16.0.0 | 255.240.0.0 | 172.16.0.1 - 172.31.255.254 |
| 192.168.0.0 | 255.255.0.0 | 192.168.0.1 - 192.168.255.254 |

* __私人__  __IP__ 只能在公司內部的區域網路使用，不可曝露到網際網路上
  * 若要對外上網、收發電子郵件的話，需要透過NAT設備
  * 使用 __公開__  __IP__ 位址的電腦可直接對外溝通。 __公開__  __IP__ 位址需事先申請
* Windows電腦若因故無法取得IP位址的話，會透過APIPA的機制來替自己設定一個169\.254\.0\.0的臨時IP位址，例如169\.254\.49\.31

