第 1 章 Active Directory網域服務\(AD DS\)

1\-1 Active Directory網域服務概觀

1\-2網域功能等級與樹系功能等級

1\-3 Active Directory輕量型目錄服務

# Active Directory網域服務

Active Directory的適用範圍

名稱空間\(Namespace\)

物件\(Object\)與屬性\(Attribute\)

容區\(Container\)與組織單位\(Organization Units\, OU\)

網域樹狀目錄\(Domain Tree\)

信任\(Trust\)

樹系\(Forest\)

架構\(Schema \)

網域控制站\(Domain Controller\)

唯讀網域控制站\(RODC\)

可重新啟動的AD DS\(Restartable AD DS\)

Active Directory資源回收筒

AD DS的複寫模式

網域中的其他成員電腦

DNS伺服器

Lightweight Directory Access Protocol\(LDAP\)

通用類別目錄\(Global Catalog\)

站台\(Site\)

目錄分割區\(Directory Partition\)

# Active Directory的適用範圍

一台電腦、一個小型區域網路\(LAN\)或數個廣域網路\(WAN\)的結合

包含此範圍中所有的物件，例如檔案、印表機、應用程式、伺服器、網域控制站與使用者帳戶等

# 名稱空間(Namespace)

* 一塊界定好的區域
  * 在此區域內，我們可以利用某個名稱來找到與這個名稱有關的資訊
* 電話簿、檔案系統是名稱空間
* Active Directory是一個名稱空間
  * 利用Active Directory，我們可以透過物件名稱來找到與這個物件有關的所有資訊
* Active Directory網域服務 \(AD DS\)與DNS緊密的整合在一起

# 物件(Object)與屬性(Attribute)

* 物件：使用者、電腦與印表機等
* 例如
  * 使用者物件：王喬治
  * 屬性：姓、名、登入帳戶、電話號碼、電子郵件帳戶、城市、國家等

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch01-Active%20Directory%E7%B6%B2%E5%9F%9F%E6%9C%8D%E5%8B%99%28AD%20DS%29_0.png)

| 物件(object) | 屬性(attributes) |
| :-: | :-: |
| 使用者(user) | 姓<br />名<br />登入帳戶<br />地址<br />… |

# 容區(Container)與組織單位(OU)

容區內可以包含其他物件，也可以包含其他容區

組織單位是特殊容區，它還有 __群組原則__ 的功能

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch01-Active%20Directory%E7%B6%B2%E5%9F%9F%E6%9C%8D%E5%8B%99%28AD%20DS%29_1.png)

# 網域樹狀目錄(Domain Tree)

最上層網域為sayms\.local，它是此網域樹狀目錄的根網域

DNS網域名稱空間有連續性

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch01-Active%20Directory%E7%B6%B2%E5%9F%9F%E6%9C%8D%E5%8B%99%28AD%20DS%29_2.png)

# 信任(Trust)

* B 網域信任 A 網域
  * 網域A的使用者可存取B網域資源
  * 網域A的使用者可利用B網域的電腦登入
* 雙向遞移性
* 父子網域自動信任

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch01-Active%20Directory%E7%B6%B2%E5%9F%9F%E6%9C%8D%E5%8B%99%28AD%20DS%29_3.png)

# 樹系(Forest)

由一或數個網域樹狀目錄所組成，每一個網域樹狀目錄都有自己唯一的名稱空間

樹系的根網域：第1個網域樹狀目錄的根網域

樹系內所有網域之間都自動雙向信任

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch01-Active%20Directory%E7%B6%B2%E5%9F%9F%E6%9C%8D%E5%8B%99%28AD%20DS%29_4.png)

# 架構(Schema)

定義物件與屬性

整個樹系內的所有網域共用一個相同的架構

只有Schema Admins群組內的使用者有權變更架構

# 網域控制站(DC)

Active Directory資料庫的存放地點

負責審核網域使用者名稱與密碼

每一台DC皆儲存著一份相同的Active Directory資料庫

異動資料會自動被複製到其他的網域控制站

多台網域控制站：分擔審核身分的負擔、容錯

由伺服器等級的電腦來扮演的，例如Windows Server 2022、Windows Server 2019、Windows Server 2016等

---

RODC：企業位於遠地的網路，如果安全措施並不像總公司一樣完備的話，很適合於使用RODC

# 唯讀網域控制站(RODC) - 1

* 只可以讀取、不可以修改
* RODC的AD DS資料庫內容只能夠從其他 __可寫式網域控制站__ 複寫過來
* RODC主要是設計給遠端分公司網路來使用
  * 分公司網路的安全措施或許並不如總公司完備，同時也可能比較缺乏IT技術人員，採用RODC可避免因其AD DS資料庫被破壞而影響到整個AD DS環境的運作

RODC的AD DS資料庫內容

單向複寫\(Unidirectional Replication\)

認證快取\(Credential Caching\)

系統管理員角色隔離\(Administrator Role Separation\)

唯讀網域名稱系統\(Read\-Only Domain Name System\)

# 可重新啟動的AD DS(Restartable AD DS)

* 在較舊版的Windows網域控制站內，需重新啟動電腦、進入 __目錄服務修復模式__ 來進行AD DS資料庫的維護工作\(例如資料庫離線重整\)
  * 重新啟動電腦將造成其所提供的所有服務被暫時中斷
* 可重新啟動的AD DS功能
  * 只需要將AD DS服務停止即可，不需重新啟動電腦來進入 __目錄服務修復模式__
  * AD DS資料庫的維護工作更容易、更快完成，且其他服務也不會被中斷

---

或譯為目錄服務還原模式，Directory Service Restore Mode

# Active Directory資源回收筒

系統管理員雖然可以進入 __目錄服務修復模式__ 來救回被誤刪的物件，但較耗費時間，而且在進入 __目錄服務修復模式__ 這段期間內，會暫停對用戶端提供服務

__Active Directory__  __資源回收筒__ 讓系統管理員不需要進入 __目錄服務修復模式__ ，就可以快速救回被刪除的物件

# AD DS的複寫模式

* __多主機複寫模式__
  * AD DS資料庫內的大部份資料是利用此模式在複寫
  * 您可以直接更新任何一台網域控制站內的AD DS物件\(例如使用者帳戶\)，之後這個更新過的物件會被自動複寫到其他網域控制站
* __單主機複寫模式__
  * 少部份資料是採用 __單主機複寫模式__ 來複寫
  * 由其中一台網域控制站\(被稱為 __操作主機__ \)負責接收與處理此要求，更新其資料庫後，再由 __操作主機__ 將它複寫給其他網域控制站
    * 例如新增或移除一個網域

# 網域中的其他成員電腦

* 成員伺服器，例如
  * Windows Server 2022 Datacenter/Standard//Essentials
  * Windows Server 2019 Datacenter/Standard//Essentials
  * Windows Server 2016 Datacenter/Standard
  * …
* 其他常用的Windows電腦，例如
  * Windows 11 Enterprise/Pro/Education
  * Windows 10 Enterprise/Pro/Education
  * Windows 8\.1 Enterprise/Pro
  * Windows 8 Enterprise/Pro
  * …

# DNS伺服器

* 網域控制站需要將自己登記到DNS伺服器內，以便讓其他電腦透過DNS伺服器來找到這台網域控制站
  * 因此需有可支援AD DS的DNS伺服器
  * 最好支援 __動態更新__ \(dynamic update\)功能，以便網域控制站可自動更新DNS伺服器內的記錄

# Lightweight Directory Access Protocol (LDAP)

* Distinguished Name \(DN\)
  * CN=林小洋\, OU=業務一組\, OU=業務部\, DC=sayms\, DC=local
* Relative Distinguished Name \(RDN\)
  * CN=林小洋

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch01-Active%20Directory%E7%B6%B2%E5%9F%9F%E6%9C%8D%E5%8B%99%28AD%20DS%29_5.png)

* Global Unique Identifier\(GUID\)
  * 每一個物件皆有一個唯一的GUID
* User Principal Name\(UPN\)
  * bob@sayms\.local
  * 無論使用者帳戶被搬移到哪一個網域，都不會改變其UPN

# 通用類別目錄(Global Catalog)

每一台網域控制站只儲存該網域內的物件

GC還儲存所有網域內所有物件的部分屬性\(常被用來查詢的屬性\)，可加快尋找物件的速度

GC也儲存萬用群組與其成員資料

當使用者利用UPN登入時，GC伺服器也負責告知網域控制站該使用者所隸屬的網域

整個樹系共用一個相同的GC

# 站台(Site)

由一或數個IP子網路所組成，網路之間透過高速且可靠的連線串接

不同站台的DC之間互相複製AD資料庫時\(會壓縮資料\)，應儘量在離峰時期複製，複製頻率不要太高

同一個站台內的DC之間的連線速度較快，因此可以有效的、快速的複製AD資料庫\(不會壓縮資料\)

AD DS會讓同一個站台內

、隸屬同一個網域的DC

之間自動複製AD資料庫

，且其預設的複製頻率也

比不同站台之間來得高

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch01-Active%20Directory%E7%B6%B2%E5%9F%9F%E6%9C%8D%E5%8B%99%28AD%20DS%29_6.png)

# 目錄分割區(Directory Partition)

* 架構目錄分割區\(Schema Directory Partition\)
  * 儲存樹系中所有物件與屬性的定義資料與建立新物件/屬性的規則
  * 樹系內所有網域共用一份相同的架構目錄分割區
* 設定目錄分割區\(Configuration Directory Partition\)
  * 儲存著整個Active Directory的結構
  * 整個樹系共用一份相同的設定目錄分割區
* 網域目錄分割區\(Domain Directory Partition\)
  * 各網域擁有自己的網域目錄分割區，其內儲存該網域的物件
* 應用程式目錄分割區\(Application Directory Partition\)
  * 由應用程式所建立，其內儲存該應用程式有關的資料

# 網域功能等級

* Windows Server 2008
  * DC：需Windows Server 2008或新版
* Windows Server 2008 R2
  * DC：需Windows Server 2008 R2或新版
* Windows Server 2012
  * DC：需Windows Server 2012或新版
* Windows Server 2012 R2
  * DC：需Windows Server 2012 R2或新版
* Windows Server 2016
  * DC：需Windows Server 2016或新版

\* Windows Server 2022、 Windows Server 2019並未新增新的網域功能等級與樹系功能等級

---

Windows Server 2003：包含Windows Server 2003與Windows Server 2003 R2

# 樹系功能等級

* Windows Server 2008
  * DC：需Windows Server 2008或新版
* Windows Server 2008 R2
  * DC：需Windows Server 2008 R2或新版
* Windows Server 2012
  * DC：需Windows Server 2012或新版
* Windows Server 2012 R2
  * DC：需Windows Server 2012 R2或新版
* Windows Server 2016
  * DC：需Windows Server 2016或新版

---

Windows Server 2003：包含Windows Server 2003與Windows Server 2003 R2

# 樹系與網域功能等級的關係

| 樹系功能等級 | 支援的網域功能等級 |
| :-: | :-: |
| Windows Server 2008 | Windows Server 2008、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2008 R2 | Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012 | Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012 R2 | Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2016 | Windows Server 2016 |

# Active Directory輕量型目錄服務

* 沒有網域的環境，也能夠擁有跟AD DS一樣的目錄服務，以便讓支援目錄存取的應用程式可以有一個目錄資料庫來儲存資料
* 可以在電腦內建立多個目錄服務的環境，每一個環境被稱為是一個 __AD LDS__  __執行個體__ \(instance\)，每一個 __AD LDS__  __執行個體__ 分別擁有獨立的目錄設定與架構\(schema\)，也分別各擁有專屬的目錄資料庫，以供支援目錄存取的應用程式來使用
* 系統管理工具
  * __Active Directory__  __輕量型目錄服務安裝精靈__
  * ADSI編輯器

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch01-Active%20Directory%E7%B6%B2%E5%9F%9F%E6%9C%8D%E5%8B%99%28AD%20DS%29_7.png)

---

此服務在Windows Server 2003(R2)內被稱為Active Directory Application Mode(ADAM)

