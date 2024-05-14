第 2 章 建立AD DS網域

2\-1 建立AD DS網域前的準備工作

2\-2 建立AD DS網域

2\-3 確認AD DS網域是否正常

2\-4 提高網域與樹系功能等級

2\-5 新增額外網域控制站與RODC

2\-6  RODC階段式安裝

2\-7 將Windows電腦加入或脫離網域

2\-8 在網域成員電腦內安裝AD DS管理工具

2\-9 移除網域控制站與網域

# 建立AD DS網域前的準備工作

* 選擇適當的DNS網域名稱
  * 符合DNS格式的網域名稱，例如sayms\.local\(以下以虛擬的 __最高層網域__ 名稱\.local為例來說明\)
* 準備好一台支援AD DS的DNS伺服器
  * Service Location Resource Record\(SRV RR\)
  * 動態更新
  * Incremental Zone Transfer\(IXFR\)
  * Fast Zone Transfer
* 選擇AD DS資料庫的儲存地點

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_0.png)

---

清除其慣用DNS伺服器的IP位址或改為輸入自己的IP位址

# DNS伺服器的架設

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_1.png)

升級為網域控制站時，順便在這台伺服器上安裝 DNS伺服

使用現有的DNS伺服器或另外安裝一台DNS伺服器，然後建立用來支援AD DS網域的區域，例如sayms\.local

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_2.png)

# 選擇AD DS資料庫的儲存地點

* 與AD DS有關的資料\(預設是位於OS的安裝磁碟\)
  * AD DS資料庫：用來儲存AD DS物件，需位於本機磁碟內，可為NTFS以增加安全性
  * 記錄檔：用來儲存AD DS資料庫的異動記錄， 需位於本機磁碟內，可為NTFS以增加安全性
  * SYSVOL資料夾：用來儲存網域共用檔案\(例如群組原則的相關檔案\)，需位於本機NTFS磁碟內
* 建議將AD DS資料庫與記錄檔分別儲存到不同硬碟內，以提高運作效率與提高復原AD DS資料庫的能力
* 若要將SYSVOL資料夾改儲存到其他磁碟
  * 需NTFS磁碟分割區
  * 可透過CONVERT指令將磁碟轉換成NTFS
    * 例如CONVERT  D:  /FS:NTFS

---

若磁碟內還有未分割的可用空間的話，可利用「磁碟管理」來建立一個新的NTFS磁碟

# 建立AD DS網域

* 建立網路中的第一台網域控制站
  * 會建立網域sayms\.local
  * 會建立網域sayms\.local所隸屬的網域樹狀目錄
  * sayms\.local ：網域樹狀目錄的根網域
  * 同時會建立一個新樹系：樹系名稱為sayms\.local
  * 網域sayms\.local ：樹系根網域

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_3.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_4.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_5.png)

# 檢查DNS伺服器內的記錄是否完備 - 1

檢查主機記錄

檢查SRV記錄\-利用DNS主控台

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_6.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_7.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_8.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_9.png)

---

網域成員電腦也會將其主機與IP位址資料登記到此區域內
若DNS伺服器是在安裝AD DS時順便安裝的，則還會建立一個名稱為_msdcs.sayms.local的區域，它是專供Windows Server網域控制站來登記的

檢查SRV記錄－利用NSLOOKUP指令

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_10.png)

「DNS request timed out…」與「預設伺服器：UnKnown」訊息\(可不理會\)：因它會根據TCP/IP處的DNS伺服器IP位址設定，來查詢DNS伺服器的主機名稱，但卻查詢不到

若不想出現此訊息的話，可將網路連線處的TCP/IPv6停用、或修改TCP/IPv6設定為「自動取得DNS伺服器位址」、或在DNS伺服器建立適當的IPv4/IPv6反向對應區域與PTR記錄

# 排除登記失敗的問題

* 主機記錄
  * IPCONFIG  /registerdns
  * 到登記失敗的電腦上執行此程式
* SRV記錄
  * 重新啟動Netlogon服務
  * 到網域控制站上啟動此服務

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_11.png)

# 檢查AD DS資料庫檔案與SYSVOL資料夾

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_12.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_13.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_14.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_15.png)

# 新增加的管理工具

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_16.png)

# 查看事件記錄檔

* 以便檢查任何與AD DS有關的問題
  * 系統管理工具事件檢視器

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_17.png)

# 提高網域與樹系功能等級

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_18.png)

| 目前的網域功能等級 | 可提升的等級 |
| :-: | :-: |
| Windows Server 2008 | Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2008 R2 | Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012 | Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012 R2 | Windows Server 2016 |

| 目前的樹系功能等級 | 可提升的等級 |
| :-: | :-: |
| Windows Server 2008 | Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2008 R2 | Windows Server 2012、Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012 | Windows Server 2012 R2、Windows Server 2016 |
| Windows Server 2012 R2 | Windows Server 2016 |

# 新增額外網域控制站與RODC

* 多台網域控制站
  * 改善使用者登入的效率
  * 容錯功能
* 兩種複寫AD DS資料庫的方式
  * 透過網路直接複寫
  * 透過安裝媒體

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_19.png)

# 唯讀網域控制站(RODC)

委派的系統管理員帳戶

允許複寫密碼至RODC的帳戶

複寫密碼至RODC被拒的帳戶

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_20.png)

---

在安裝網域中的第1台RODC時，系統會自動建立與RODC有關的群組帳戶，這些帳戶會自動被複寫給其他網域控制站，不過可能需花費一點時間，尤其是複寫給位於不同站台的網域控制站。之後您在其他站台安裝RODC時，若安裝精靈無法從這些網域控制站得到這些群組資訊的話，它會顯示警告訊息，此時請等這些群組資訊完成複寫後，再繼續安裝這台RODC

# 利用「安裝媒體」來安裝額外網域控制站 - 1

* 製作「安裝媒體」
  * 將AD DS資料庫儲存到 __安裝媒體__ 內，並將其複寫到隨身碟、CD、DVD等媒體或共用資料夾內。然後在安裝額外網域控制站時從 __安裝媒體__ 來讀取AD DS資料庫
  * 可以大幅降低對網路所造成的衝擊

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_21.png)

其中的 __sysvol__ 表示要製作內含ntds\.dit與SYSVOL的 __安裝媒體__ ； __full__ 表示要製作供可寫式網域控制站使用的 __安裝媒體__ ，若是要製作供RODC使用的安裝媒體的話，請將 __full__ 改為 __rodc__

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_22.png)

# 變更RODC的委派與密碼複寫原則設定 - 1

Active Directory使用者和電腦

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_23.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_24.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_25.png)

Active Directory管理中心

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_26.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_27.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_28.png)

# RODC階段式安裝

* 這兩個階段分別由不同的使用者來完成，通常是用來安裝遠端分公司所需的RODC
  * 第1階段：建立RODC帳戶
    * 通常是在總公司內執行，且只有系統管理員\(Domain Admins\)群組才有權利。系統管理員需在AD DS資料庫內替RODC建立電腦帳戶、設定選項、將第2階段的安裝工作委派給指定的使用者或群組
  * 第2階段：將伺服器附加到RODC帳戶
    * 通常是在遠端分公司內執行，被委派者有權利在此階段來完成安裝RODC的工作
    * 被委派者不需具備網域系統管理員權限。若沒有委派其他使用者或群組的話，則只有Domain Admins或Enterprise Admins群組有權利

# 建立RODC帳戶

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_29.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_30.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_31.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_32.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_33.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_34.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_35.png)

# 將伺服器附加到RODC帳戶

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_36.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_37.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_38.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_39.png)

# 可以被加入網域的電腦

Windows Server 2016 Datacenter/Standard/Essentials

Windows Server 2012\( R2\) Datacenter/Standard

Windows Server 2008\(R2\) Datacenter/Enterprise/Standard

Windows 10 Enterprise/Pro/Education

Windows 8\.1\(8\) Enterprise/Pro

Windows 7 Ultimate/ Enterprise/Professional

Windows Vista Ultimate/Enterprise/Business

# 將Windows電腦加入網域 - 1

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_40.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_41.png)

Windows 10:點擊下方的 __檔案總管__ 圖示對著 __本機__ 按右鍵內容點擊右方的 __變更__  __設定__

Windows 8\.1:切換到 __開始__ 選單點擊選單左下方  符號對著 __本機__ 按右鍵點擊下方 __內容__

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_42.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_43.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_44.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_45.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_46.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_47.png)

一般網域使用者帳戶只有10次將電腦加入網域的機會，但網域系統管理員沒有限制

# 利用已加入網域的電腦登入

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_48.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_49.png)

# 離線加入網域

從Windows 7開始的用戶端可在未與網域控制站連線的情況下，就被加入網域

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_50.png)

先到一台已經加入網域的電腦上執行djoin\.exe

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_51.png)

到即將加入網域的離線電腦win10pc2上利用djoin\.exe來將上述檔案內的資訊匯入到win10pc2

當win10pc2可以與網域控制站連線時，請重新啟動win10pc2，便完成了加入網域的程序

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_52.png)

# 脫離網域

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_53.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_54.png)

# 在網域成員電腦內安裝AD DS管理工具 - 1

Windows Server 2016、Windows Server 2012\(R2\)成員伺服器

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_55.png)

Windows Server 2008 R2、Windows Server 2008成員伺服器

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_56.png)

* Windows 10、Windows 8\.1、Windows 8
  * 下載與安裝「Remote Server Administration Tools for Windows 10\(8\.1、8\)」
  * 位於統管理工具內

* Windows 7
  * 下載與安裝「Remote Server Administration Tools for Windows 7 with SP1」
  * 開始控制台點擊最下方的 __程式集__ 點擊最上方的 __開啟或關閉__  __Windows__  __功能__ 勾選 __遠端伺服器管理工具__ 之下的 __Active__   __Directory__  __管理中心__

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_57.png)

# 移除網域控制站與網域 - 1

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_58.png)

* 若網域內還有其他網域控制站存在的話
  * 會被降級為該網域的成員伺服器，例如dc2\.sayms\.local
* 若這台網域控制站是此網域內的最後一台網域控制站
  * 例如假設dc2\.sayms\.local已被降級，此時再將dc1\.sayms\.local降級…
  * Enterprise Admins群組才有權利移除網域內的最後一台網域控制站\(移除網域\)
* 若是 __通用類別目錄伺服器__ 的話，請檢查其站台內是否還有其他 __通用類別目錄伺服器__ ，若沒有的話，請先指派另外一台網域控制站來扮演 __通用類別目錄伺服器__ ，否則將影響使用者登入
* 若所移除的網域控制站是樹系內最後一台網域控制站的話，則樹系會一併被移除\(需Enterprise Admins群組的成員才有權利\)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_59.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_60.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_61.png)

降級後，再執行一次移除角色的動作

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch02-%E5%BB%BA%E7%AB%8BAD%20DS%E7%B6%B2%E5%9F%9F_62.png)

