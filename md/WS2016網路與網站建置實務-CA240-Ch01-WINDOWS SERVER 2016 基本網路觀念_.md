第 1 章 Windows Server 2016基本網路觀念

1\-1 Windows Server 2016的網路功能

1\-2 TCP/IP通訊協定簡介

1\-3 IPv6基本觀念

1\-4 Windows Server 2016的管理工具

# 建置各種不同架構的網路

* 企業內部網路\(Intranet\)
  * 私人區域網路\(LAN\)
* 網際網路\(Internet\)
  * 為企業提供一個電子商業服務的網路環境
* 企業外部網路\(Extranet\)
  * 將區域網路與客戶、供應商、合作夥伴的網路透過網際網路串接起來
* 遠端存取
  * 讓使用者可以遠端存取區域網路的資源。兩個位於不同地點的區域網路，也可以透過 __虛擬私人網路__ \(VPN\)串接在一起

# Windows Server 2016的網路服務

同時支援IPv4與IPv6

DHCP伺服器、DNS伺服器、WINS伺服器

PKI（Public Key Infrastructure）與IPsec（Internet Protocol Security）

路由及遠端存取、RADIUS伺服器與DirectAccess

路由器、NAT與虛擬私人網路（VPN）

Quality of Service （QoS）

Windows防火牆、802\.1X無線網路、遠端桌面服務、Windows部署服務

IIS網站、SSL網站、FTP伺服器、SSL FTP伺服器

Windows Server Update Services（WSUS）

網路負載平衡（Network Load Balancing）與Web Farm

…

# TCP/IP通訊協定簡介

* 是目前最完整、最被廣泛支援的通訊協定、也是Windows Server 2016網路依賴最深的通訊協定
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
| D |   |   | 224-239 |   |   |
| E |   |   | 240-254 |   |   |

\*127\.0\.0\.1供 __迴路測試__ 、也代表主機本身； 第1個IP位址代表網路本身、最後1  個代表廣播位址； Class D是 __多點__  __傳送__ 的群組識別碼； Class E保留給特殊用途或實驗用途

# TCP/IP網路範例

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_0.png)

# 子網路遮罩

| Class | 預設子網路遮罩(2進位) | 預設子網路遮罩(10進位) |
| :-: | :-: | :-: |
| A | 11111111  00000000  00000000  00000000 | 255.0.0.0 |
| B | 11111111  11111111  00000000  00000000 | 255.255.0.0 |
| C | 11111111  11111111  11111111  00000000 | 255.255.255.0 |

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_1.png)

* CIDR（Classless Inter\-Domain Routing\)無等級的劃分方式
  * 192\.168\.1\.0/24
  * 10\.120\.0\.0/16

# 預設閘道

一般主機若要與其他網路的主機溝通的話，需要透過路由器來轉送

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_2.png)

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
  * 只能夠利用它來與同一個網路內IP位址也是169\.254\.0\.0格式的電腦溝通

# IPv6基本觀念

IPv6位址的語法

IPv6位址分類

IPv6位址的自動設定

# IPv6位址的語法

* IPv4
  * 192\.168\.1\.31
  * 32個位元、分為4個區塊、每個區塊佔用8個位元、區塊之間利用句點\(\.\)隔開、十進位
* IPv6
  * 128個位元、分為8個區塊、每個區塊佔用16個位元、區塊之間利用冒號\(:\)隔開、 十六進位

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_3.png)

# 前導0可以省略

00D4 → D4、0000 → 0、03AC → 3AC

區塊中只有靠左邊的0可以被省略，例如F570不可以改寫為F57

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_4.png)

# 連續的0區塊可以縮寫

可以改用雙冒號\(::\)來代表這些連續區塊

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_5.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_6.png)

# IPv6的首碼(prefix)

* 為IPv6位址的一部分，用來表示IP位址中某些位元是固定的值或用來反應其所代表的子網路
* 首碼表示方式與IPv4的CIDR表示方式相同
  * _位址_  _/_  _首碼長度_ ：例如21DA:D3:0:2F3B::/64，表示最左邊64個位元固定為21DA:D3:0:2F3B
* IPv4內使用的子網路遮罩，在IPv6內已經不支援

# IPv6位址分類

* Unicast位址\(單點傳播位址\)
  * Global unicast 位址、Link\-local位址、Site\-local位址、Unique Local IPv6 Unicast位址、特殊位址、相容位址
* Multicast位址\(多點傳播位址\)
* Anycast位址\(任一傳播位址\)

| IPv4位址 | IPv6位址 |
| :-: | :-: |
| 網際網路位址等級式分類 | 不分等級 |
| Public IP位址 | Global unicast位址 |
| Private IP位址(10.0.0.0/8、172.16.0.0/12與192.168.0.0/16) | Site-local位址(FEC0::/10)或Unique Local IPv6 Unicast位址(FD00::/8) |
| APIPA自動設定的IP位址(169.254.0.0/16) | Link-local位址(FE80::/64) |
| Loopback位址為127.0.0.1 | Loopback位址為::1 |
| 未指定位址為0.0.0.0 | 未指定位址為:: |
| 廣播位址 | 不支援廣播 |
| 多點傳播位址(224.0.0.0/4) | IPv6多點傳播位址(FF00::/8) |

# Global unicast位址 - 1

* 相當於IPv4的public IP位址，可被路由與連接到網際網路

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_7.png)

* 最左邊3個位元固定為001，首碼為2000::/3
* Global Routing Prefix\(全域路由首碼\)
  * 公司網路所在地\(站台\)的路由首碼，類似於IPv4的網路識別碼
  * 3個固定為001的首碼 \+ 45個位元的Global Routing Prefix，一共48個位元被用來指派給公司行號的站台
  * 網際網路的IPv6路由器可路由首碼符合這48個位元格式的封包
* Subnet ID\(子網路識別碼\)
  * 用來區分站台內的子網路，讓公司行號在一個站台內建立最多65536個子網路

* Interface ID\(介面識別碼\)
  * 表示子網路內的一個網路介面，相當於IPv4的主機識別碼
* Interface ID可以透過以下兩種方式之一來產生
  * 根據網路卡的MAC位址來產生
    * Windows Server 2003與Windows XP的預設值
  * 亂數產生Interface ID
    * Windows Server 2016/2012 R2/2012/2008 R2/2008、Windows 10/8\.1/8/7/Vista的預設值

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_8.png)

# Link-local位址 - 1

使用此位址來與同一 __連結__ \(link，子網路\)上的節點溝通

相當於IPv4中利用APIPA機制取得的IP位址\(169\.254\.0\.0/16\)

IPv6節點\(例如Windows Server 2016\)會自動設定link\-local位址

首碼為FE80::/64

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_9.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_10.png)

* 網路介面的索引值\(interface index\)
  * 因link\-local位址的前置碼可以重複使用，因此位於不同連結內的節點，其link\-local位址也可以相同，這會造成使用上的混淆
  * 圖中連結1內的電腦1與連結2內的電腦2的link\-local位址相同
  * 若要在伺服器上利用ping指令來與連接2內的電腦2溝通時，要如何讓這台伺服將封包從網路卡2來送出呢? 在ping指令後面加上介面索引值\(圖中的值為12\)：
  * 例如 Ping   FE80::10DF:D9F4:DE2D:3691%12

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_11.png)

---

Site-local位址前置碼可以重複使用

* Zone ID
  * link\-local位址:網路介面索引值
  * site\-local位址:site ID
* ipconfig或ipconfig /all

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_12.png)

# Site-local位址

* 相當於IPv4中的private IP位址\(10\.0\.0\.0/8，172\.16\.0\.0/12與192\.168\.0\.0/16\)
* 只能夠用來與同一站台內的節點溝通
* 必須透過路由器或DHCPv6伺服器來設定
* RFC3879內已經不建議在新建置的IPv6網路使用site\-local位址，但是現有IPv6環境可以繼續使用site\-local位址
  * 建議改用 __Unique __  __Local IPv6 Unicast__  __位__  __址__
* 首碼FEC0::/10
* 利用54位元的Subnet ID來劃分子網路

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_13.png)

# Unique Local IPv6 Unicast位址

用來取代Site\-local位址，企業內的所有節點都能使用此具備唯一性的位址

其首碼為 __FC00::/__  __7__ 。L\(Local\)=1，首碼為 __FD00::/__  __8__

40個位元的Global ID用來區別企業內的每一個站台，它是隨機值

Subnet ID\(子網路識別碼\)用來區分站台內的子網路，讓企業在一個站台內建立最多65536個子網路

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_14.png)

# 特殊位址

* 未指定位址\(unspecified address\)
  * 0:0:0:0:0:0:0:0或::，相當於IPv4的0:0:0:0
  * 不會被用來指定給網路介面，也不會被當作封包的目的位址
  * 當節點要確認其網路介面所獲得的暫時位址是否唯一時，它所送出的確認封包的來源位址就是使用 __未指定位址__
* 迴路位址\(loopback address\)
  * 0:0:0:0:0:0:0:1或::1，相當於IPv4的127\.0\.0\.1
  * 可透過此位址來執行迴路測試，以便檢查網路卡與驅動程式是否可以正常運作

# 相容位址與自動通道

* 將IPv4網路轉移到IPv6是一個漫長與深具挑戰的工作
* IPv6提供了數個自動通道技術與相容IP位址來協助從IPv4轉移到IPv6
  * 將IPv6封包封裝到IPv4的封包內，然後透過IPv4網路內的“IPv6 over IPv4通道”來傳送

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_15.png)

# 相容位址的種類 - 1

* ISATAP位址\(Intra\-Site Automatic Tunnel Addressing Protocol\)
  * 使用在主機\-主機、主機\-路由器、路由器\-主機之間
  * 可供兩台同時支援IPv6與IPv4的主機來溝通
  * Interface ID格式為::0:5EFE:w\.x\.y\.z，w\.x\.y\.z為unicast IPv4位址\(public或private\)
  * 任何可用在unicast位址的64位元首碼，都可以當作ISATAP 位址的首碼，包含link\-local位址\(FE80::/64\)，例如FE80::5EFE:192\.168\.8\.128
  * Windows Server 2016的每一個IPv4網路介面都有一個虛擬ISATAP通道介面，而系統預設會自動替此介面設定一個link\-local ISATAP 位址
* 6to4位址
  * 使用在路由器\-路由器、主機\-路由器、路由器\-主機之間
  * 讓IPv6主機或路由器可以透過IPv4網際網路來連接
  * 屬於global unicast位址，其首碼為2002:wwxx:yyzz::/48，其中的wwxx:yyzz 擷取自unicast public IPv4位址\(w\.x\.y\.z\)

# 相容位址的種類 - 2

* Teredo位址
  * 可供位於IPv4的NAT之後、且同時支援IPv6與IPv4的主機來使用
  * 首碼為 __2001::/__  __32__
* IPv4\-compatible位址
  * 兩台同時支援IPv6與IPv4的主機需要經過IPv4網路\(使用public位址\)來溝通的話，就可以使用IPv4\-compatible位址
  * IPv4\-compatible位址的格式為0:0:0:0:0:0:w\.x\.y\.z或::w\.x\.y\.z，其中的w\.x\.y\.z為unicast IPv4位址\(public\)
  * 例如某台主機的IPv4位址為140\.115\.8\.1，則其IPv4\-compatible位址為 __0:0:0:0:0:0:140\.115\.8\.1__ 或 __::140\.115\.8\.1__

---

參閱Microsoft安全性公告MS06-064

# Multicast位址(多點傳播位址) - 1

* 多個節點可以加入到同一multicast群組內，一個節點也可以加入多個multicast群組
* 最高8個位元固定為11111111\(十六進位FF\)
* Flags：0000、0001
* Scope：multicast位址可傳送的範圍
  * 路由器根據scope來決定是否要路由此multicast封包
  * 常見值為1\(node\-local scope\)、2\(link\-local scope\)、5\(site\-local scope\)
  * 例如路由器收到一個要傳送到FF02::2\(link\-local\)的封包時，並不會將此封包傳送到超出此區域連結\(local link\)以外的連結
* Group ID：代表此群組的唯一群組識別碼
  * 佔用112個位元，RFC 3513建議只使用最低的32個位元，其餘的填0

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_16.png)

---

Flags=0000：由IANA(Internet Assigned Numbers Authority)固定指派給well-known multicast位址的位址；Flags=0001：是尚未被IANA固定指派使用的暫時multicast位址

# Solicited-node multicast位址

* IPv4利用ARP request來解析IP位址，此廣播封包會干擾網路
* IPv6是透過送出Neighbor Solicitation訊息來解析IP位址
  * 為了減少對連結內節點的干擾，因此訊息目的地不是採用link\-local scope all\-nodes multicast位址，而是solicited\-node multicast位址
* 此位址是從網路介面的unicast位址轉換而來，其首碼為FF02::1:FF00:0/104，最後的24個位元擷取自unicast位址的Interface ID的最右24個位元
  * 例如link\-local位址FE80::10DF:D9F4:DE2D:369B，其最右邊24個位元為2D:369B，故其solicited\-node multicast位址為FE02::1:FF2D:369B
* IPv6不再使用廣播位址\(改採multicast位址\)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_17.png)

# Anycast位址(任一傳播位址)

* 可被指派給多個網路節點，送到此位址的封包，並不會被傳送到擁有此位址的所有節點，而是只會被傳送到路由距離最近的節點
* 此位址目前只能用在封包目的地位址，而且只能指派給路由器來使用
* 使用IPv6 unicast位址\(沒有自己的IPv6格式\)，但在指派Anycast位址給路由器來使用時，需指明其為Anycast位址
* 目前唯一被定義的Anycast位址為： __Subnet\-Router anycast__  __位址__ ，它是路由器必須支援的位址，傳送給此位址的封包，會被傳送到該子網路中的一個路由器
  * 用戶端可以透過傳送Subnet\-Router anycast封包來找尋路由器
  * 此位址格式中的subnet prefix表示網路介面所在的連結\(link，子網路\)，其長度視不同的unicast位址而有所不同，後面剩下的位元都是0

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_18.png)

# 自動設定IPv6位址的方法

* IPv6主機預設會自動替每一個網路介面設定一個link\-local位址
* 若IPv6主機能夠找到路由器的話，還可以根據路由器內的設定來獲得更多IPv6位址與選項
  * global位址
    * 可用來連接網際網路
  * site\-local位址或Unique Local IPv6 Unicast位址
    * 可用來連接同一個站台內的其他子網路
* IPv6主機是透過送出Router Solicitation訊息來尋找路由器，路由器會回應Router Advertisement訊息

# Router Advertisement訊息內容

* 一或多個額外的首碼
  * IPv6主機會根據這些額外的首碼\(可能是global或local首碼\)來另外建立一或多個IPv6位址
* Managed Address Configuration\(M\)旗標
  * M=1：表示要使用DHCPv6來取得IPv6位址
* Other Stateful Configuration\(O\)旗標
  * O=1：表示要使用DHCPv6來取得其他選項，例如DNS伺服器的IPv6位址

# 無狀態與有狀態自動設定

* __M=0 & __  __O=0__
  * 僅會根據路由器所傳來的首碼來建立一或多個IPv6位址\(或手動設定\)，此狀況被稱為 __無狀態位址自動設定__ \(stateless address autoconfiguration\)。此時IPv6主機需透過其他方式來設定選項，例如手動設定
* __M=0 & O=1__
  * IPv6主機還會透過DHCPv6來取得其他選項
* __M=1 & O=0__
  * IPv6主機還會透過DHCPv6來取得其他IPv6位址，此狀況被稱為 __有狀態位址自動設定__ \(stateful address autoconfiguration\)。此時IPv6主機需透過其他方式來設定選項，例如手動設定
* __M=1 & O=1__
  * 還會透過DHCPv6來取得其他IPv6位址與選項

# 自動設定的IPv6位址的狀態分類 - 1

* IP位址在不同的時機有著不同狀態
  * 不論是IPv6主機自動設定的link\-local位址、或利用路由器傳回的首碼所建立的global或local位址、還是透過DHCPv6取得的任何一個IPv6位址

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_19.png)

* Tentative\(暫時性\)
  * 新產生的IPv6位址是處於tentative 狀態
  * IPv6主機會透過送出Neighbor Solicitation訊息來執行DAD程序，若收到Neighbor Advertisement回應訊息，就將此位址標示為 __已經被重複使用__
* Preferred\(偏好的\)
  * 若未收到回應訊息，就將此位址的狀態改為Preferred，而從現在開始它就是有效的\(valid\)IPv6位址，IPv6主機可以利用此位址來接收與傳送封包
* Deprecated\(已過時\)
  * Preferred IPv6位址的使用期限過後，其狀態就會被改為Deprecated，它還是一個有效的位址，現有的連線可以繼續使用Deprecated位址，不過新的連線不應該使用
* Invalid\(無效的\)
  * 處於Deprecated狀態的位址在經過一段時間後就會變成無效的\(invalid\)位址

# IPv6位址狀態分類範例圖

netsh interface ipv6 show address指令

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_20.png)

# 公用與臨時IPv6位址

* 公用IPv6位址\(public IPv6 address\)
  * global位址，主要用來接收連入連線，例如用在網站
  * 應該要在DNS伺服器內登記
  * 其interface ID可以是EUI\-64位址或利用亂數產生
* 臨時IPv6位址\(temporary IPv6 address\)
  * 主要是用戶端應用程式在起始連線時使用，例如網頁瀏覽器就可以使用此位址來對外連接網站
  * 不需要在DNS伺服器內登記
  * 其interface ID是亂數產生，每次IPv6通訊協定啟動時，其IPv6位址都不一樣，如此可避免使用者的上網行為被追蹤

# Interface ID的產生方式

netsh interface ipv6 set global randomizeidentifiers=disabled

netsh interface ipv6 set global randomizeidentifiers=enabled

netsh interface ipv6 show global

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_21.png)

# Windows Server 2016的管理工具

* 在安裝伺服器角色\(例如DNS伺服器\)後，系統便會自動建立用來管理這些角色的工具
* 可以透過以下途徑來執行它們
  * 點擊左下角 __開始__ 圖示   Windows 系統管理工具
  * 點擊左下角 __開始__ 圖示   伺服器管理員點擊右上方的 __工具__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_22.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_23.png)

在Windows Server 2016 /2012 R2/2012安裝管理工具

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_24.png)

# 在Windows Server 2008(R2)安裝管理工具

* 透過 __伺服器__  __管理員__ 的 __新增__  __角色及__  __功能__
  * 完成後可到 __系統管理工具__ 來執行這些工具

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_25.png)

# 在Windows 10/8.1/8/7安裝管理工具

* Windows 10\(8\.1/8\)：到微軟網站下載與安裝Remote Server Administration Tools for Windows 10\(8\.1/8\)
  * 10：【點擊左下角 __開始__ 圖示   Windows系統管理工具】
  * 8\.1：【按Windows鍵切換到 __開始__ 選單點擊選單左下方  圖案系統管理工具】
  * 8：透過【按Windows鍵切換到 __開始__ 選單點擊 __系統管理工具__ 】的途徑來執行這些工具

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_26.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_27.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch01-WINDOWS%20SERVER%202016%20%E5%9F%BA%E6%9C%AC%E7%B6%B2%E8%B7%AF%E8%A7%80%E5%BF%B5_28.png)

* Windows 7：到微軟網站下載與安裝Remote Server Administration Tools for Windows 7 with SP1
  * 控制台點擊最下方 __程式集__ 點擊最上方 __開啟或關閉__  __Windows__  __功能__ 勾選 __遠端伺服器管理工具__ 之下所需的角色或功能管理工具
  * 透過【開始系統管理工具】來執行這些工具

