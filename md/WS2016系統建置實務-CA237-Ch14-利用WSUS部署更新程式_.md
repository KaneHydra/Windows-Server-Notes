第 14 章 利用WSUS部署更新程式

14\-1 WSUS概觀

14\-2 WSUS的系統需求

14\-3 WSUS的特性與運作方式

14\-4 安裝WSUS伺服器

14\-5 設定用戶端的自動更新

14\-6 核准更新程式

14\-7 自動更新的群組原則設定

# 14-1 WSUS概觀

* 可以透過以下方式
  * 手動連接Microsoft Update網站
  * 透過Windows系統的自動更新功能
* 可能會有以下的缺點
  * 影響網路效率
  * 與現有軟體相互干擾
* WSUS\(Microsoft Windows Server Update Services\)可以解決上述問題

# 透過WSUS伺服器集中下載更新程式

可漸少對網路效率的影響

測試更新程式，確定對企業內部電腦無不良影響後，再透過網管人員的核准程序，將其部署到用戶端電腦

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_0.png)

# 系統需求

* WSUS伺服器
  * Microsoft Report Viewer Redistributable 2012
    * 用來製作各種不同的報告
    * 到Microsoft網站下載
  * Microsoft System CLR Types for SQL Server 2012
    * 裝Microsoft Report Viewer Redistributable 2012前需要先安裝Microsoft System CLR Types for SQL Server 2012
* WSUS伺服器的系統磁碟分割區\(system partition\)與安裝WSUS的磁碟分割區的檔案系統都必須是NTFS

---

系統磁碟分割區與安裝WSUS的磁碟分割區的檔案系統都必須是NTFS

# 「WSUS管理主控台」的需求

* WSUS伺服器已內含WSUS __管理主控台__
* 其他Windows系統需另外安裝
  * Windows Server 2016
    * 透過 __伺服器管理員__ 的 __新增角色及功能__
  * Windows 10
    * 下載與安裝Remote Server Administration Tools for Windows 10\(Windows 10的遠端伺服器管理工具\)
* 還必須安裝
  * Microsoft System CLR Types for SQL Server 2012
  * Microsoft Report Viewer Redistributable 201

# 14-3 WSUS的特性與運作方式

* 將用戶端電腦適當分組，可以更容易與明確的將更新程式部署到指定的電腦
* 內建電腦群組
  * 所有電腦
  * 尚未指派的電腦

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_1.png)

# WSUS伺服器的架構

* 自治模式
  * 下游伺服器從上游伺服器取得更新程式，但不包含更新程式的核准狀態、電腦群組資訊
  * 下游伺服器需自行決定是否要核准這些更新程式與自行建立所需的電腦群組
* 複本模式
  * 下游伺服器會從上游伺服器取得更新程式、更新程式的核准狀態與電腦群組資訊
  * 所有可在上游伺服器上管理的項目均無法在下游伺服器自行管理
  * 電腦群組資訊只有電腦群組本身而已，並不包含成員

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_2.png)

# 上下游伺服器的好處

* 降低網際網路連線的負擔
  * 上游伺服器下載更新程式，然後將它分配給其餘的下游伺服器
* 分散管理
  * 大型網路可透過多台下游WSUS伺服器來服務不同的用戶端
* 用戶端電腦更快取得更新程式
  * 將更新程式放到較接近用戶端電腦的下游伺服器
  * ＊下游伺服器所需的語言，上游伺服器需先安裝
  * ＊上下游串接最好不要超過3層，因為會增加延遲時間，因而拉長將 更新程式傳遞到每一台伺服器的時間

# 選擇資料庫

* Windows Server 2016內建資料庫或Microsoft SQL Server 2008 R2 SP1\(或新版\)
* 每一台WSUS伺服器都有自己獨立的資料庫
* 資料庫內容
  * WSUS伺服器的設定資訊
  * 描述每一個更新程式的metadata
    * 更新程式的屬性
    * 適用規則
    * 安裝資訊
  * 用戶端電腦與更新程式之間的關係

# 選擇更新程式檔案的儲存地點

* 儲存在WSUS伺服器的本機硬碟內
  * WSUS伺服器會從Microsoft Update網站\(或上游伺服器\)下載更新程式
  * 用戶端可直接從WSUS伺服器來取得更新程式
  * WSUS伺服器最少需10GB的可用空間，建議40GB以上
* 儲存在Microsoft Update網站
  * 同步時， WSUS伺服器只會從Microsoft Update網站下載更新程式的Metadata資料，並不會下載更新程式本身
  * 核准用戶端可以安裝某個更新程式後，用戶端是直接連接Microsoft Update網站來下載該更新程式
  * 適用於用戶端電腦的數量不多，或用戶端與WSUS伺服器之間的連線速度不快，但卻與網際網路之間的連線速度較快時

---

硬碟容量視更新程式數量、所下載的語言數量、產品種類數量等因素而定


# 延緩下載更新程式

WSUS伺服器會先下載更新程式的metadata，而更新程式檔案只有在被核准後才會下載

可節省網路頻寬與WSUS伺服器的硬碟空間

它是建議值與預設值

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_3.png)

# 使用「快速安裝檔案」

* 用戶端只下載新版與舊版之間的差異
  * 可減少用戶端從WSUS伺服器下載的資料量、降低對企業內部網路的負擔
  * WSUS伺服器從Microsoft Update下載的 __快速安裝檔案__ 比較大，因其包含新更新程式與各舊版檔案之間的差異，因此WSUS伺服器在下載檔案時會比較佔用對外的網路頻寬

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_4.png)

# 14-4 安裝WSUS伺服器

* 建議採用Active Directory網域環境
  * 可利用群組原則來充分控管用戶端的自動更新設定
* 下載與安裝Microsoft System CLR Types for SQL Server 2012  SP2
  * 內含在Microsoft® SQL Server® 2012 Feature Pack，檔名是SQLSysClrTypes\.msi
* 下載與安裝Microsoft Report Viewer 2012 Runtime
* 透過 __伺服器__  __管理員__ 的 __新增角色及功能__ 來安裝Windows Server Update Services

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_5.png)

# 第1次同步處理

可選擇安裝後來執行第1次同步工作

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_6.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_7.png)

# 後續的同步處理工作

* 手動同步
* 排程自動同步
  * 點選右邊的 __同步處理選項__ ，或是
  * 透過 __選項__ 畫面中的 __同步處理排程__
* 安裝過程中的所有設定也都可透過 __選項__ 畫面來變更

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_8.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_9.png)

---

在同步動作尚未完成之前，無法儲存您所變更的設定

# 14-5 設定用戶端的自動更新

* 群組原則
  * 在Active Directory網域環境下，可以透過 __群組原則__ 來讓網域內的電腦從指定的WSUS伺服器下載更新程式
* 本機電腦原則
  * 若沒有Active Directory網域環境，或是用戶端電腦未加入網域的話，可以透過 __本機電腦原則__ 來設定

# 群組原則 - 自動更新

* 電腦設定原則系統管理範本Windows元件Windows Update
  * 通知我下載和通知我安裝
  * 自動下載和通知我安裝
  * 自動下載和排程安裝
  * 允許本機系統管理員選擇設定

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_10.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_11.png)

# 群組原則 – 指定WSUS伺服器

讓用戶端從指定的WSUS伺服器下載更新

將更新結果回報給指定的WSUS伺服器

電腦套用原則後才有效

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_12.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_13.png)

---

未加入網域的電腦可透過本機電腦原則(GPEDIT.MSC)來完成上述設定，而且設定完成後會立刻套用


# 佈署更新程式

* 用戶端套用原則完成後，需與WSUS伺服器開始接觸後，在WSUS __管理主控台__ 才看得到這些用戶端電腦、才可將更新程式佈署到這些電腦
* 用戶端何時與WSUS伺服器接觸？
  * 群組原則套用完成後約20分鐘
  * 在用戶端電腦上執行wuauclt /detectnow指令

# 14-6 核准更新程式

* 利用WSUS主控台來監控與管理用戶端的更新程式
* 透過最上方左邊的 __狀態__ 處選擇根據不同的狀態來顯示用戶端電腦的資訊
  * 用戶端可利用 __wuauclt__  __  /__  __reportnow__ 指令來立即回報最新狀態

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_14.png)

---

若用戶端電腦仍未顯示在此畫面：到這些電腦上執行gpupdate /force、再執行wuauclt /detectnow指令，以便加快讓這些電腦出現畫面中

# 建立新電腦群組

* 讓在 __WSUS__  __管理主控__  __台__ 中佈署用戶端更新程式更為方便
  * 尤其是電腦數量較多時
* 將應隸屬於新群組的電腦，從 __尚未指派的電腦__ 搬移到此群組

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_15.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_16.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_17.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_18.png)

# 核准更新程式的安裝

* 更新程式需要經過核准後，該更新程式才會被下載，用戶端電腦才可以安裝此程式
  * 之後若要解除核准的話，請選擇 __未核准__

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_19.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_20.png)

\* 「 __已安裝或不適用的百分比__ 」欄位的數值\(圖中為75%\)，表示「已經安裝此更新程式與不     適用此更新程式」的電腦數量，佔所有電腦數量總數的百分比

---

例如總共有100台電腦，其中有60台電腦已經安裝了此更新程式、15台電腦不適用此更新程式，則此處的數值就是(60+15)/100=75%

# 未下載完成前的警示

  * 伺服器預設會延緩下載更新程式，也就是同步時僅會下載更新程式的metadata，待更新程式被核准後，才會被下載
  * 由於才剛核准更新程式，伺服器正要開始下載此更新程式，在還未下載完成之前，都會看到如圖中的提示訊息，需等伺服器下載完成後，用戶端電腦才可以開始安裝此更新程式

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_21.png)

---

圖中更新程式右邊核准欄位出現了安裝(1/3)字樣，表示目前有3個電腦群組，其中有1個群組已經被核准安裝此更新程式

# 檢查最新更新程式的間隔時間

* 預設每隔17\.6 到 22 小時連接WSUS伺服器來檢查是否有最新的更新程式可供下載
  * 也可利用 __wuauclt__  __ /__  __detectnow__ 指令來手動檢查
* 若有新更新程式的話，會根據圖所示的設定來下載與安裝更新程式

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_22.png)

# 變更檢查更新的間隔時間

* 間隔時間為 __頻率值__ 減掉一個隨機值
  * 隨機值 = 頻率值的0% 到 20 %
* 預設值
  * 頻率值 22小時
  * 實際值17\.6 – 22 小時

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_23.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_24.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_25.png)

# 拒絕更新程式

系統將解除其核准

在WSUS資料庫內與此更新有關的報告資料\(由用戶端電腦送來的\)都將被刪除

在 __核准__ 處改選擇 __拒絕__ 可看到被拒絕的更新程式

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_26.png)

# 自動核准更新程式 - 1

* 讓WSUS伺服器與Windows Update同步時，自動核准所下載的更新程式
* 例如要將下載的 __安全性更新__ 與 __重大更新__ 自動核准給所有電腦的話
  * 可勾選 __預設的自動核准規則__
  * __執行規則__ :將此規則套用到已經同步的更新程式

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_27.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_28.png)

# 自動核准更新程式 - 2

* WSUS更新
  * 是否要讓WSUS產品本身的更新程式自動被核准
* 更新的修訂
  * 自動核准已核准更新的新修訂
    * 自動拒絕新修訂版本會使更新過期的更新

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_29.png)

# 14-7 自動更新的群組原則設定

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_30.png)

# 自動更新的群組原則設定 - 1

* 設定自動更新
* 指定近端內部網路Microsoft更新服務的位置
* 自動更新偵測頻率
* 允許非系統管理員收到更新通知
  * 啟用:非系統管理員也會在右下角的狀態列收到通知訊息
  * 停用或不設定\(預設\):只有系統管理員才會收到此通知訊息
* 允許立即安裝自動更新
  * 更新程式下載完成且準備好可以安裝時，預設是根據 __設定自動更新__ 的設定來決定何時安裝更新程式
  * 啟用此原則會將自動安裝那些既不會中斷Windows服務，也不會重新啟動Windows 系統的更新程式

* 有使用者登入時不自動重新開機以完成排定的自動更新安裝
  * 在有使用者登入的請況下，排程安裝更新程式完成後，僅會通知已經登入的使用者，不自動重新啟動電腦
* 延遲排程安裝的重新啟動
  * 預設為5分鐘後自重新啟動電腦
* 再次提示排程安裝所需的重新啟動
  * 預設為10分鐘後

* 重新排程已經排程好的自動更新安裝
  * 重新啟動後，何時安裝之前錯過時間的更新程式
    * 啟用:依照指定時間過後
    * 停用:等下一次排程的時間到達時
    * 不設定:1分鐘後

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_31.png)

* 啟用用戶端目標鎖定
  * 自動將用戶端加入到指定的電腦群組
* 允許來自內部網路Microsoft更新服務位置的已簽署更新
  * 讓用戶端電腦可以從WSUS伺服器下載由其他協力廠商所開發與簽署的更新程式

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_32.png)

* 移除「Window Update」的連結並存取
  * 移除 __開始__ 功能表的 __Window Update__ 連結
  * 移除Internet Explorer的 __工具__ 功能表的 __Window __  __Update__ 連結
  * __控制台__ 的 __Windows Update__ 內的 __檢查更新__ 也會變成灰色
* 關閉所有Windows Update功能的存取
  * 用戶端電腦將無法連接Microsoft Update網站與取得更新程式，不過還是可以從WSUS伺服器來取得

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_33.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch14-%E5%88%A9%E7%94%A8WSUS%E9%83%A8%E7%BD%B2%E6%9B%B4%E6%96%B0%E7%A8%8B%E5%BC%8F_34.png)

---

可避免本機系統管理員自行透過開始功能表的Windows Update連結等
用戶端透過開始功能表的Windows Update連結、直接輸入網址都會被拒絕

