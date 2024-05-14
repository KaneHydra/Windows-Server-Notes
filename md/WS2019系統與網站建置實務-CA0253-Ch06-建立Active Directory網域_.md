第 6 章 建立Active Directory網域

6\-1 Active Directory網域服務

6\-2 建立Active Directory網域

6\-3 將Windows電腦加入或脫離網域

6\-4 管理Active Directory網域使用者帳戶

6\-5 管理Active Directory網域群組帳戶

6\-6 提高網域與樹系功能等級

6\-7 Active Directory資源回收筒

6\-8 移除網域控制站與網域

# 6-1 Active Directory網域服務

Active Directory的適用範圍

名稱空間\(Namespace\)

物件\(Object\)與屬性\(Attribute\)

容區\(Container\)與組織單位\(Organization Units\, OU\)

網域樹狀目錄\(Domain Tree\)

信任\(Trust\)

樹系\(Forest\)

架構\(Schema \)

網域控制站\(Domain Controller\)

Lightweight Directory Access Protocol \(LDAP\)

通用類別目錄\(Global Catalog\)

站台\(Site\)

網域功能與樹系功能

目錄分割區\(Directory Partition\)

# Active Directory的適用範圍

一台電腦、一個小型區域網路\(LAN\)或數個廣域網路\(WAN\)的結合

包含此範圍中所有的物件，例如檔案、印表機、應用程式、伺服器、網域控制站與使用者帳戶等

# 名稱空間(Namespace)

* 一塊界定好的區域
  * 我們可以利用某個名稱來找到與這個名稱有關的資訊
* 電話簿、檔案系統是名稱空間
* Active Directory是名稱空間
  * 利用Active Directory，我們可以透過物件名稱來找到與這個物件有關的所有資訊
* Active Directory網域服務\(AD DS\)與DNS緊密的整合

# 物件(Object)與屬性(Attribute)

* 物件：使用者、電腦與印表機等
* 例如
  * 使用者物件：王喬治
  * 屬性：姓、名、登入帳戶、電話號碼、城市、國家等

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_0.png)

| 物件(object) | 屬性(attributes) |
| :-: | :-: |
| 使用者(user) | 姓<br />名<br />登入帳戶<br />地址<br />… |

# 容區(Container)與組織單位(OU)

容區內可以包含其他物件，也可以包含其他容區

組織單位是特殊容區，它還有 __群組原則__ 的功能

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_1.png)

# 網域樹狀目錄(Domain Tree)

最上層網域為sayms\.local，它是根網域

DNS網域名稱空間有連續性

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_2.png)

# 信任(Trust)

* B 網域信任 A 網域
  * 網域A的使用者可存取B網域資源
  * 網域A的使用者可利用B網域電腦登入
* 雙向遞移性
* 父子網域自動信任

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_3.png)

# 樹系(Forest)

一或數個網域樹狀目錄所組成，每一個網域樹狀目錄都有自己唯一的名稱空間

樹系的根網域：第1個網域樹狀目錄的根網域

樹系內所有網域之間都自動雙向信任

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_4.png)

# 架構(Schema)

定義物件與屬性

樹系內所有網域共用一個相同的架構

只有Schema Admins群組有權變更架構

# 網域控制站(DC)

* Active Directory資料庫的存放地點
* 負責審核網域使用者名稱與密碼
* 每一台DC皆儲存著一份相同的Active Directory資料庫
* 異動資料會自動被複製到其他的網域控制站
* 多台網域控制站：分擔審核身分的負擔、容錯
* 由伺服器等級的電腦來扮演的，例如Windows Server 2019、Windows Server 2016、Windows Server 2012\(R2\)
* 唯讀網域控制站\(RODC\)
  * 只可以讀取、不可以修改

---

RODC：企業位於遠地的網路，如果安全措施並不像總公司一樣完備的話，很適合於使用RODC

# Lightweight Directory Access Protocol (LDAP)

* Distinguished Name \(DN\)
  * CN=林小洋\, OU=業務一組\, OU=業務部\, DC=sayms\, DC=local
* Relative Distinguished Name \(RDN\)
  * CN=林小洋

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_5.png)

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

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_6.png)

# 網域功能等級

* Windows Server 2008
  * 網域控制站需Windows Server 2008或新版
* Windows Server 2008 R2
  * 網域控制站需Windows Server 2008 R2或新版
* Windows Server 2012
  * 網域控制站需Windows Server 2012或新版
* Windows Server 2012 R2
  * 網域控制站需Windows Server 2012 R2或新版
* Windows Server 2016
  * 網域控制站需Windows Server 2016或新版

\* Windows Server 2019並未新增新的網域功能等級與樹系功能等級

---

Windows Server 2003：包含Windows Server 2003與Windows Server 2003 R2

# 樹系功能等級

* Windows Server 2008
  * 網域控制站需Windows Server 2008或新版
* Windows Server 2008 R2
  * 網域控制站需Windows Server 2008 R2或新版
* Windows Server 2012
  * 網域控制站需Windows Server 2012或新版
* Windows Server 2012 R2
  * 網域控制站需Windows Server 2012 R2或新版
* Windows Server 2016
  * 網域控制站需Windows Server 2016或新版

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

# 目錄分割區(Directory Partition)

* 架構目錄分割區\(Schema Directory Partition\)
  * 儲存樹系中所有物件與屬性的定義資料與新物件/屬性的建立規則
  * 樹系內所有網域共用一份相同的架構目錄分割區
* 設定目錄分割區\(Configuration Directory Partition\)
  * 儲存著整個Active Directory的結構
  * 整個樹系共用一份相同的設定目錄分割區
* 網域目錄分割區\(Domain Directory Partition\)
  * 各網域擁有自己的網域目錄分割區，其內儲存該網域的物件
* 應用程式目錄分割區\(Application Directory Partition\)
  * 由應用程式所建立，其內儲存該應用程式有關的資料

# 6-2 建立Active Directory網域

建立網域的必要條件

建立網路中的第一台網域控制站

檢查DNS伺服器內的記錄是否完備

建立更多的網域控制站

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_7.png)

# 建立網域的必要條件

* DNS網域名稱
  * 例如sayms\.local
* DNS伺服器

# 建立網路中的第一台網域控制站 - 1

* 建立第一台網域控制站server1\.sayms\.local時
  * 會建立網域sayms\.local
  * 會建立網域sayms\.local所隸屬的網域樹狀目錄
  * sayms\.local ：網域樹狀目錄的根網域
  * 同時建立一個新樹系，樹系名稱sayms\.local
  * 網域sayms\.local ：樹系根網域
  * 透過伺服管理員或Windows Admin Center

  * 透過伺服管理員或Windows Admin Center

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_8.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_9.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_10.png)

# 檢查DNS伺服器內的記錄是否完備

檢查主機記錄與SRV記錄

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_11.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_12.png)

---

網域成員電腦也會將其主機與IP位址資料登記到此區域內

# 排除登記失敗的問題

* 主機記錄
  * IPCONFIG  /registerdns
  * 到登記失敗的電腦上執行此程式
* SRV記錄
  * 重新啟動Netlogon服務
  * 到網域控制站上啟動此服務

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_13.png)

# 建立更多的網域控制站

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_14.png)

* 多台網域控制站
  * 改善使用者登入的效率
  * 容錯功能
* 透過 __伺服器管理員__

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_15.png)

\* 只有Enterprise Admins或Domain Admins內   的使用者有權建立其他網域控制站

# 6-3 將Windows電腦加入或脫離網域

<span style="color:#AB0043"> __將__ </span>  <span style="color:#AB0043"> __Windows__ </span>  <span style="color:#AB0043"> __電腦加入網域 __ </span>  <span style="color:#AB0043"> __\- 1__ </span>  <span style="color:#AB0043"> __ __ </span>

Windows Server 2019 Datacenter/Standard

Windows Server 2016 Datacenter/Standard

Windows Server 2012 R2 Datacenter/Standard

Windows Server 2012 Datacenter/Standard

Windows 10 Enterprise/Pro/Education

Windows 8\.1 Enterprise/Pro

Windows 8 Enterprise/Pro

Windows 7 Ultimate/ Enterprise/Professional

# 將Windows電腦加入網域 - 2

Windows Server 2019、 Windows Server 2016等

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_16.png)

* Windows 10
  * 開啟 __檔案總管__ 對著 __本機__ 按右鍵內容點擊右方的 __變更__  __設定__
* Windows 8\.1
  * 按Windows鍵切換到 __開始__ 選單點擊選單左下方  符號對著 __本機__ 按右鍵點擊下方的 __內容__ 點擊 __變更__  __設定__
* Windows 8
  * 按鍵切換到 __開始__ 選單對著空白處按右鍵點擊 __所有應用程式__ 對著 __電腦__ 按右鍵點擊下方 __內容__ …
* Windows 7
  * 開始對著 __電腦__ 按右鍵內容點擊右下角的 __變更設定__

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_17.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_18.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_19.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_20.png)

# 利用已加入網域的電腦登入

* 本機帳戶：
  * 直接輸入Administrator的密碼，或點擊其他使用者電腦名稱\\使用者帳戶
* 網域帳戶：
  * 點擊其他使用者網域名稱\\使用者帳戶或UPN

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_21.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_22.png)

# 脫離網域

* 需Enterprise Admins、Domain Admins或本機系統管理員
  * 若沒有權利的話，系統會先要求輸入帳戶與密碼

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_23.png)

---

開始對著電腦按右鍵內容點擊變更設定點選工作群組輸入適當的工作群組名稱，完成後重新啟動電腦

# 6-4 管理Active Directory網域使用者帳戶

Active Directory使用者和電腦 或 Active Directory管理中心

第1台網域控制站原本機帳戶會轉移到AD資料庫的Users容區、其他網域控制站的原本機帳戶會被刪除

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_24.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_25.png)

# 其他成員電腦的Active Directory管理工具

Windows Server 2019、2016等

Windows Server 2008\(R2\)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_26.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_27.png)

* Windows 10 、Windows 8\.1、Windows 8
  * 下載與安裝Remote Server Administration Tools for Windows 10\(8\.1/8\)
* Windows 7
  * 下載與安裝Remote Server Administration Tools for Windows 7 with SP1
  * 控制台程式集開啟或關閉Windows功能遠端伺服器管理工具勾選 __Active Directory__  __管理中心__

# 建立組織單位(OU)與網域使用者帳戶

* 可利用 __Active Directory__  __管理中心__ 或 __Active Directory__  __使用者和電腦__
* 網域使用者帳戶
  * UPN：例如george@sayms\.local
  * SamAccountName\(Windows 2000前版\)：例如SAYMS\\george
* 網域使用者的密碼預設值
  * 至少需7個字元
  * 不可包含使用者帳戶名稱或全名
  * 至少要包含以下4組字元中的3組：A – Z、a – z、0 – 9、非字母數字\(例如\! $\#、%\)

# 登入測試

* 到網域內任何一台非網域控制站的成員電腦上來登入網域
  * 在登入畫面中點擊左下方的 __其他使用者__
* 可利用以下名稱登入
  * 使用者SamAccountName：SAYMS\\george
  * UPN：george@sayms\.local

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_28.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_29.png)

---

一般使用者帳戶預設無法在網域控制站上登入，除非另外開放

# Hyper-V加強的工作階段模式

網域使用者在網域成員電腦上需被加入到本機Remote Desktop Users群組內，否則無法登入

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_30.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_31.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_32.png)

# 賦予使用者在網域控制站登入的權利

* 可以在網域控制站登入的使用者
  * 需擁有在網域控制站上 __允許__  __本機__  __登入__ 的權利
  * 預設只有網域Administrators等少數群組的成員擁有此權利
* 到任一網域控制站上透過 __群組原則管理__  __Default __  __Domain Controllers Policy__ 來設定

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_33.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_34.png)

# 原則套用後才有效

* 需等原則設定值被套用到網域控制站
  * 將網域控制站重新開機
  * 等網域控制站自動套用：需等5分鐘或更久
  * 手動套用：到網域控制站上執行以下指令
  * __   gpupdate __ 或  __gpupdate /force__

# 多台網域控制站的情況

* 群組原則設定值是先被儲存到扮演  __PDC__  __操作主機__ 角色的網域控制站
  * 預設是第1台網域控制站
  * 可以透過 __Active Directory__  __使用者和電腦__ 來查看
* 需讓這些設定值從 __PDC__  __操作主機__ 複製到其他網域控制站與套用

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_35.png)

---

開始Windows系統管理工具Active Directory使用者和電腦對著網域名稱sayms.local按右鍵操作主機PDC標籤

# 群組原則的複製時機

* 自動複製
  * __PDC__  __操作主機__ 預設是15秒後會自動將其複製給同一個站台內的其它網域控制站
* 手動複製
  * 到任一網域控制站上透過 __Active Directory__  __站台及服務__

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_36.png)

---

手動複製：同一個站台內的網域控制站之間會隔15秒自動複製，不需要手動複製，除非發生特殊情況，或是您希望不同站台之間的網域控制站能夠立刻複製的話，才有需要採用手動複製


# 資料異動的起始儲存地點

* 群組原則設定：先被儲存在 __PDC__  __操作主機__
* Active Directory物件：先被儲存在目前所連接的網域控制站
* 若要改連接到其他網域控制站的話
  * Active Directory管理中心：點擊 __變更網域控制站__
  * Active Directory使用者和電腦：對著 __Active Directory__  __使用者和電腦__ 按右鍵變更網域控制站

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_37.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_38.png)

# 登入疑難排除

* 若在網域控制站上登入時出現如圖所示的畫面
  * 未具備 __允許本機登入__ 的權利
    * 尚未開放此權利
    * 設定值尚未從PDC操作主機複製到此網域控制站或尚未套用

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_39.png)

# 網域使用者個人資料的設定

以 __Active Directory__  __管理中心__ 為例

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_40.png)

# 限制登入時段

用來設定使用者何時可以登入到網域

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_41.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_42.png)

# 限制登入電腦

只要網域使用者具備 __允許本機登入__ 的權利，則預設可利用網域內的任何一台電腦來登入網域

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_43.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_44.png)

---

電腦名稱可為NetBIOS名稱(例如server3)或DNS名稱(例如server3.sayms.local)

# 6-5 管理Active Directory網域群組帳戶

* 網域內的群組類型
  * 安全性群組:可被設定權限、可寄郵件給此群組
  * 發佈群組:無法被設定權限、可寄郵件給此群組
* 群組的使用領域
  * 網域本機群組
  * 全域群組
  * 萬用群組

# 群組特性

|  | 網域本機群組 | 全域群組 | 萬用群組 |
| :-: | :-: | :-: | :-: |
| 可包含的成員 | 所有網域內的使用者、全域群組、萬用群組；相同網域內的網域本機群組 | 相同網域內的使用者與全域群組 | 所有網域內的使用者、全域群組、萬用群組 |
| 可以在哪一個網域內被設定使用權限 | 同一個網域 | 所有網域 | 所有網域 |
| 群組轉換 | 可以被換成萬用群組(只要是原群組內的成員不含網域本機群組即可) | 可以被換成萬用群組(只要是原群組不隸屬於任何一個全域群組即可) | 可以被換成網域本機群組；可以被換成全域群組(只要是原群組內的成員不含萬用群組即可) |

# 網域群組的建立與管理

對著任何一個容區或組織單位按右鍵新增群組

新增群組的成員

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_45.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_46.png)

---

網域使用者帳戶與群組帳戶也都有唯一的安全識別碼(security identifier，SID)

# 內建的網域本機群組

Account Operators

Administrators

Backup Operators

Guests

Network Configuration Operators

Performance Monitor Users

Print Operators

Remote Desktop Users

Server Operators

Users

# 內建的全域群組與萬用群組

* 全域群組
  * Domain Admins
  * Domain Computers
  * Domain Controllers
  * Domain Users
  * Domain Guests
* 萬用群組
  * Enterprise Admins
  * Schema Admins
* 特殊群組
  * Everyone、Authenticated Users、Interactive、Network、Anonymous Logon

---

特殊群組：與第4章介紹的相同


# 6-6 提高網域與樹系功能等級

| 目前的網域功能等級 | 可提升的等級 |
| :-: | :-: |
| Windows Server 2008 | Windows Server 2008 R2、Windows Server 2012 、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2008 R2 | Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012  | Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012 R2 | Windows Server 2016 |

| 目前的樹系功能等級 | 可提升的等級 |
| :-: | :-: |
| Windows Server 2008 | Windows Server 2008 R2、Windows Server 2012 、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2008 R2 | Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012  | Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012 R2 | Windows Server 2016 |

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_47.png)

# AD LDS

* Active Directory輕量型目錄服務\(AD LDS\)
  * 在沒有網域的環境下，仍然可享有目錄服務與目錄資料庫的好處
  * 可建立多個目錄服務的環境，每一個環境稱為一個 __AD LDS__  __執行個體__  __\(Instance\)__
  * 每一個執行個體擁有獨立的目錄設定、架構、目錄資料庫
* 透過 __伺服器管理員__ 來新增 __Active Directory__  __輕量型目錄服務__ 角色
* Windows系統管理工具
  * 建立 __AD LDS__  __執行個體__
    * Active Directory輕量型目錄服務安裝精靈
  * 管理 __AD LDS__  __執行個體__ 內的目錄設定、架構、物件
    * ADSI編輯器

---

安裝AD LDS的途徑為：【開啟伺服器管理員點擊角色點擊新增角色…選擇 Active Directory輕量型目錄服務…】，

# 6-7 Active Directory資源回收筒

* 讓你輕易的從資源回收筒救回被誤刪的物件
* 樹系與網域功能等級需為Windows Server 2008 R2\(含\)以上
  * 所有網域控制站都需為Windows Server 2008 R2\(含\)以上
* 啟用 __Active Directory__  __資源回收筒__ 後，就無法再停用，因此網域與樹系功能等級也都無法再被降級

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_48.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_49.png)

\* 還原組織單位、還原其內的物件

# 6-8 移除網域控制站與網域

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch06-%E5%BB%BA%E7%AB%8BActive%20Directory%E7%B6%B2%E5%9F%9F_50.png)

* 透過降級的方式來移除網域控制站
  * 將Active Directory 從網域控制站移除
* 若網域內還有其他網域控制站
  * 會被降級為該網域的成員伺服器。Domain Admins或Enterprise Admins
* 網域內最後一台網域控制站降級
  * 降級後，網域會被移除，該電腦也會被降級為獨立伺服器。Enterprise Admins

* 若網域控制站是 __通用類別目錄伺服器__
  * 若所屬站台內沒有其他通用類別目錄伺服器的話，請先另外指派一台網域控制站來扮演通用類別目錄伺服器
  * 透過Active Directory站台及服務
* 樹系內最後一台網域控制站降級
  * 降級後，樹系會被移除，而該電腦也會被降級為獨立伺服器。Enterprise Admins

