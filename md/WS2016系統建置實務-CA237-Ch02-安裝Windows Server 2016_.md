第 2 章

安裝Windows Server 2016

2\-1 安裝前的注意事項

2\-2 安裝或升級為Windows Server 2016

2\-3 啟動與使用Windows Server 2016

# 2-1 安裝前的注意事項

Windows Server 2016的安裝選擇

* 含桌面體驗的伺服器
  * 安裝標準的圖形化使用者介面，並支援所有的服務與工具
* Server Core
  * 可降低維護與管理需求、減少使用硬碟容量、減少被攻擊面。沒有視窗管理介面，只能使用 __命令提示字元__ 、Windows PowerShell或透過遠端電腦來管理此台伺服器。這是微軟的 __建議__ 選項
* Nano Server
  * 比 Server Core較小、只支援 64 位元應用程式與工具。需透過遠端來管理。已針對私有雲和資料中心最佳化。佔用的磁碟空間更少、設定的速度明顯地更快，而且所需的更新和重新啟動次數更少

# Windows Server 2016的系統需求

| 元件 | 需求 |
| :-: | :-: |
| 處理器(CPU) | 最少1.4GHz、64位元；支援NX或DEP；支援CMPXCHG16B、 LAHF/SAHF與PrefetchW；支援SLAT(EPT或NPT) |
| 記憶體(RAM) | 「含桌面體驗的伺服器」最少需 2GB |
| 硬碟 | 最少32GB |

---

實際上的需求要看電腦設定、所安裝的應用程式、所扮演的角色與所安裝的功能等    數量的多寡而可能需要增加

# 使用coreinfo.exe程式

可到https://technet\.microsoft\.com/網站搜尋與下載coreinfo\.exe程式

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_0.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_1.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_2.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_3.png)

# 選擇磁碟分割區-1

磁碟需被分割成一或數個磁碟分割區

若磁碟完全未經過分割\(例如全新磁碟\)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_4.png)

---

以MBR磁碟為例

# 選擇磁碟分割區-2

可以將未分割磁碟的部分空間劃分出一個磁碟分割區

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_5.png)

# 選擇磁碟分割區-3

若磁碟分割區內已經有其他作業系統的話

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_6.png)

原Windows系統會被取代，不過原來大部分的系統設定會被保留在新系統內，一般的資料檔案\(非作業系統檔案\)也會被保留

現有Windows系統所在的資料夾\(一般是Windows\)會被搬移到Windows\.old資料夾

---

若您在安裝過程中將現有磁碟分割區刪除或格式化的話，則該分割區內的現有資料都將遺失

# 選擇磁碟分割區-4

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_7.png)

# Windows Server 2016的檔案系統

* NTFS
  * 支援較大的磁碟分割區、檔案權限、檔案加密、檔案壓縮、稽核與磁碟配額等
  * Windows Server 2016只可以安裝到NTFS磁碟分割區內
* ReFS
  * 提供較高的安全性、更大的磁碟容量與較佳的效能
  * 不支援檔案壓縮、不支援磁碟配額
* FAT/FAT32
  * 舊的檔案系統
  * 無法將Windows Server 2016安裝到此處，但可當作資料碟
  * 無檔案權限設定、檔案加密等功能
* exFAT
  * 適合於抽取式儲存設備

# 2-2 安裝或升級為Windows Server 2016

* 全新安裝
  * 利用USB隨身碟\(或DVD\)啟動系統與安裝程式
  * 在現有的Windows系統內啟動USB隨身碟\(或DVD\)內的安裝程式
* 將現有的前版Windows作業系統升級
  * 在此Windows系統\(x64\)內啟動USB隨身碟\(DVD\)內的安裝程式

\* 若要製作內含Windows Server 2016的USB隨身碟的話，可以上網找工具，例如Windows USB/DVD Download Tool、WinToFlash

---

Windows系統(x64)：Windows Server 2008 R2、64位元的Windows Server 2008同等級的版本

# 2-3 啟動與使用Windows Server 2016

啟動與登入Windows Server 2016

* 首次登入需使用系統管理員身分\(Administrator\)，且需設定密碼
  * 至少需6個字元
  * 不可包含使用者帳戶名稱或全名
  * 至少要包含以下4組字元中的3組：
    * A – Z
    * a – z
    * 0 – 9
    * 非字母數字\(\!、$、\#、%\)等
* 例如12abAB為有效密碼、123456為無效密碼

# 伺服器管理員

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_8.png)

# Windows開機管理程式

若同時安裝了多套作業系統的話，則開機時會出現Windows Boot Manager畫面，此時請在30秒內從清單中選擇欲啟動的Windows系統，否則會自動啟動預設的系統\(最新安裝的系統\)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_9.png)

# 驅動程式需經過簽章

* __隨插即用__ \(PnP\)裝置的驅動程式需經過簽章，否則系統不會載入此程式，若因而系統不正常運作或無法啟動的話，可停用 __驅動程式強制簽章__ 要求
  * 啟動電腦時，在完成自我測試、系統啟動初期立刻按F8鍵
  * 執行Bcdedit\.exe程式

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_10.png)

# 登出、鎖定電腦與關機

* 若暫時不想使用此電腦，但是又不想關機的話
  * __登出__ ：它會結束目前正在執行的應用程式。之後若要繼續使用此電腦的話，需重新登入
  * __鎖定__ ：鎖定期間所有的應用程式仍然會繼續執行。若要解除鎖定，以便繼續使用此電腦的話，需輸入密碼

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_11.png)

# 將電腦關機或重新啟動

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_12.png)

按Ctrl \+ Alt \+ Del鍵

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202016_13.png)

