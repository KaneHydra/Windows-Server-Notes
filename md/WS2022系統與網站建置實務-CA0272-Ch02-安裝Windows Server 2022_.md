第 2 章

安裝Windows Server 2022

2\-1 安裝前的注意事項

2\-2 新安裝或升級

2\-3 啟動與使用Windows Server 2022

# 2-1 安裝前的注意事項

<span style="color:#AB0043"> __Windows Server 2022__ </span>  <span style="color:#AB0043"> __的安裝選擇__ </span>

* 含桌面體驗的伺服器
  * 標準的圖形化使用者介面，並支援所有的服務與工具
* Server Core
  * 可降低管理需求、減少使用硬碟容量、減少被攻擊面。沒有視窗管理介面，只能使用Windows PowerShell、 __命令提示字元__ 或透過遠端電腦來管理此台伺服器。這是微軟的 __建議__ 選項。
  * __Server Core __  __應用程式相容性__  __FOD__ ：安裝後，支援一些圖形介面的管理工具
* Nano Server
  * 類似於 Server Core，但明顯較小。Windows Server 2022僅在容器（container）內支援Nano Server

# Windows Server 2022的系統需求

| 元件 | 需求 |
| :-: | :-: |
| 處理器(CPU) | 最少1.4GHz、64位元；支援NX或DEP；支援CMPXCHG16B、 LAHF/SAHF與PrefetchW；支援SLAT(EPT或NPT) |
| 記憶體(RAM) | 512MB ，「含桌面體驗的伺服器」最少需 2GB |
| 硬碟 | 最少32GB |

\* 實際的需求要看電腦設定、所安裝的應用程式、所扮演的角色與所安裝的功能等數量的多寡而可能需要增加

---

實際上的需求要看電腦設定、所安裝的應用程式、所扮演的角色與所安裝的功能等    數量的多寡而可能需要增加

# 使用coreinfo.exe程式

可到https://technet\.microsoft\.com/網站搜尋與下載coreinfo\.exe程式

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_0.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_1.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_2.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_3.png)

# 選擇磁碟分割區-1

磁碟需被分割成一或數個磁碟分割區

若磁碟完全未經過分割\(例如全新磁碟\)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_4.png)

---

以MBR磁碟為例

# 選擇磁碟分割區-2

可以將未分割磁碟的部分空間劃分出一個磁碟分割區

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_5.png)

# 選擇磁碟分割區-3

若磁碟分割區內已經有其他作業系統的話

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_6.png)

原Windows系統會被取代，不過原來大部分的系統設定會被保留在新系統內，一般的資料檔案\(非作業系統檔案\)也會被保留

現有Windows系統所在的資料夾\(一般是Windows\)會被搬移到Windows\.old資料夾

---

若您在安裝過程中將現有磁碟分割區刪除或格式化的話，則該分割區內的現有資料都將遺失

# 選擇磁碟分割區-4

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_7.png)

磁碟分割區需被格式化成適當的檔案系統後，才可以在其內安裝作業系統與儲存資料

檔案系統包含NTFS、ReFS、FAT32、FAT與exFAT等

只能將Windows Server 2022安裝到NTFS磁碟分割區內，其他類型的磁碟僅能用來儲存資料

# 2-2 安裝或升級為Windows Server 2022

* 全新安裝
  * 利用USB隨身碟\(或DVD\)啟動系統與安裝程式
  * 在現有的Windows系統內啟動USB隨身碟\(或DVD\)內的安裝程式
* 將現有的前版Windows作業系統升級
  * 在此Windows系統內啟動USB隨身碟\(DVD\)內的安裝程式

\* 若要製作內含Windows Server 2022的USB隨身碟的話，可以上網找工具，例如Rufus、Windows USB/DVD Download Tool

---

Windows系統(x64)：Windows Server 2008 R2、64位元的Windows Server 2008同等級的版本

# 安裝Windows Server 2022

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_8.png)

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_9.png)

# 2-3 啟動與使用Windows Server 2022

<span style="color:#AB0043"> __啟動與登入__ </span>  <span style="color:#AB0043"> __Windows Server 2022__ </span>

* 首次登入需使用系統管理員身分\(Administrator\)，且需設定密碼
  * 至少需6個字元
  * 不可包含使用者帳戶名稱或全名
  * 至少要包含以下4組字元中的3組：
    * A – Z、a – z、0 – 9、非字母數字\(\!、$、\#、%\)等
* 例如12abAB為有效密碼、123456為無效密碼

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_10.png)

# 伺服器管理員

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_11.png)

# 登出、鎖定電腦與關機

* 若暫時不想使用此電腦，但是又不想關機的話
  * __登出__ ：它會結束目前正在執行的應用程式。之後若要繼續使用此電腦的話，需重新登入
  * __鎖定__ ：鎖定期間所有的應用程式仍然會繼續執行。若要解除鎖定，以便繼續使用此電腦的話，需輸入密碼

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_12.png)

# 將電腦關機或重新啟動

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_13.png)

按Ctrl \+ Alt \+ Del鍵

![](WS2022%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0272-Ch02-%E5%AE%89%E8%A3%9DWindows%20Server%202022_14.png)

