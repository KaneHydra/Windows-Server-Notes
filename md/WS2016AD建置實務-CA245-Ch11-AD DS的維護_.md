第 11 章 AD DS的維護

11\-1 系統狀態概觀

11\-2 備份AD DS

11\-3 復原AD DS

11\-4  AD DS資料庫的搬移與重整

11\-5 重設「目錄服務修復模式」的系統管理員密碼

11\-6 變更「可重新啟動的AD DS 」的登入設定

11\-7 Active Directory資源回收筒

# 系統狀態所包含的資料

登錄值

COM\+ 類別註冊資料庫\(Class Registration database\)

啟動檔案\(boot files\)

Active Directory憑證服務\(AD CS\)資料庫

AD DS資料庫\(Ntds\.dit\)

SYSVOL資料夾

叢集服務資訊

Microsoft Internet Information Services\(IIS\) metadirectory

受Windows Resource Protection保護的系統檔案

# AD DS資料庫

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_0.png)

* AD DS資料庫檔案
  * ntds\.dit：AD DS資料庫檔案
  * edb\.log：AD DS異動記錄檔
  * db\.chk：檢查點檔案
  * edbres00001\.jrs與edbres00002\.jrs：預留檔案
* SYSVOL資料夾
  * 位於% _systemroot_ %內，此資料夾內儲存著以下的資料： __指令檔__ \(scripts\)、 __NETLOGON__  __共用資料夾__ 、 __SYSVOL__  __共用資料夾__ 與 __群組原則相關設定__

# 備份AD DS

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_1.png)

* 安裝Windows Server Backup功能
* 備份系統狀態
  * 系統狀態位於安裝Windows系統的磁碟內，一般是C:磁碟
  * 備份 __目的地磁碟__ 預設是不可以包含來源磁碟\(C:\)
    * 以下假設備份到\\\\dc2\\backup
  * 需隸屬於Administrators或Backup Operators群組，且需有權限將資料寫入目的地磁碟或共用資料夾

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_2.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_3.png)

* 也可以透過 __wbadmin__ 指令來備份系統狀態，例如：
  * __wbadmin__  __  start  __  __systemstatebackup__  __  \-__  __backuptarget__  __:\\\\dc2\\backup__

---

若要開放可以備份到來源磁碟的話，請在以下登錄路徑新增一筆名稱為AllowSSBToAnyVolume的數值，其類型為DWORD：HKLM\SYSTEM\CurrentControlSet\Services\wbengine\SystemStateBackup
其值為1表示開放，為0表示禁止。建議不要開放，否則可能會備份失敗，而且需要使用比較多的磁碟空間

# 復原AD DS

若AD DS資料損毀的話，可以透過執行 __非授權還原__ \(nonauthoritative restore\)的程序來修復AD DS

必須進入 __目錄服務修復模式__ \(Directory Services Restore Mode，DSRM，或譯為 __目錄服務還原模式__ \)，然後利用之前的備份來執行 __非授權還原__ 的工作

若系統無法啟動的話，則應該執行完整伺服器的復原程序

# 進入「目錄服務修復模式」的方法

* 在 __命令__  __提示字元__ 或 __Windows PowerShell__ 視窗下執行
  * __Bcdedit__  __  /set  \{__  __bootmgr__  __\}  __  __displaybootmenu__  __  Yes__

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_4.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_5.png)

若電腦內安裝了多套Windows系統的話，它會自動顯示上述畫面

也可透過 __bcdedit__  __ /set __  __safeboot__  __ __  __dsrepair__  、 __bcdedit__  __  /__  __deletevalue__  __  __  __safeboot__ 指令

也可以在網域控制站上透過重新開機、完成自我測試後、系統啟動初期立刻按F8鍵

---

若是使用虛擬機器的話，按F8鍵前先確認焦點是在虛擬機器上

# 執行AD DS的「非授權還原」

* 輸入 __目錄服務修復模式__ 的系統管理員的使用者名稱與密碼來登入
  * __\.\\__  __Administrator__ 或 _電腦名稱_  __\\Administrator__ 。

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_6.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_7.png)

# 透過wbadmin.exe程式來復原

* 請先讀取備份的版本號碼
  * __wbadmin__  __  get  versions  –__  __backuptarget__  __:\\\\__  __dc2\\backup__
  * 記下欲用來復原的備份版本，它是位於 __版本識別碼__ 處的字串\(假設是 __09/12/2017\-06:42__ \)
  * 若在儲存備份的位置內儲存著多台伺服器的備份的話，則您可以指定欲讀取的伺服器，例如增加 __\-machine:dc1 __ 參數
* 接著執行以下指令
  * __wbadmin__  __  start  __  __systemstaterecovery__  __  –__  __version:__  __09/12/2017\-06:42__  __ __  __–__  __backuptarget__  __:\\\\dc2\\backup__

# 非授權還原與授權還原

* 要還原AD DS內的物件時，若網域內只有一台網域控制站，則只需執行 __非授權還原__ 即可，但若有多台的網域控制站的話，則可能還需搭配 __授權__  __還原__
* 例如兩台網域控制站DC1與DC2
  * 備份網域控制站DC2的系統狀態
  * 誤刪使用者帳戶 __王__  __喬治__
  * 此異動資料被複寫到網域控制站DC1
  * 因此在DC1內的 __王喬治__ 帳戶也會被刪除
* 嘗試救回被誤刪的 __王喬治__ 帳戶
  * 在DC2上利用一般還原方式\( __非__  __授權__  __還原__ \)將備份的舊 __王喬治__ 帳戶復原
  * DC2內的 __王喬治__ 帳戶已被復原了，但在DC1內的 __王喬治__ 卻是被標記為 __已__  __刪除__
  * 下一次DC1與DC2之間執行複寫程序時，DC2內剛被復原的 __王喬治__ 帳戶會被刪除，因為DC1內被標記為 __已刪除__ 的 __王喬治__ 的版本號碼較高，而DC2內剛復原的舊 __王喬治__ 的版本號碼較低
* 若要救回 __王喬治__ 帳戶的話，需要另外再執行 __授權__  __還原__
  * 在DC2上針對 __王喬治__ 另外執行 __授權__  __還原__ 後，此被復原的舊 __王__  __喬治__ 的版本號碼將被增加\(從備份當天開始到執行 __授權還原__ 為止，每天增加100\,000\)
  * 因此當DC1與DC2開始執行複寫程序時，由於位於DC2的舊 __王喬治__ 帳戶的版本號碼會比較高，所以這個舊 __王喬治__ 會被複寫到DC1，將DC1內被標記為 __已刪除__ 的 __王喬治__ 覆蓋掉，也就是說舊 __王喬治__ 被復原了

\* 使用者帳戶被刪除後，它並不會立刻從AD DS資料庫內移除，而是被搬移到一個名稱為 __Deleted Objects__ 的容區內，同時這個使用者帳戶的版本號碼會被加1。預設是180天後才會將其從AD DS資料庫內移除

# 授權還原的演練步驟

在網域控制站DC2建立組織單位 __業務部__ 、在 __業務部__ 內建立使用者帳戶 __王喬治__ \(George\)

等組織單位 __業務部__ 、使用者帳戶 __王喬治__ 帳戶被複寫到網域控制站DC1

在網域控制站DC2備份 __系統狀態__

在網域控制站DC2上將使用者帳戶 __王喬治__ 刪除\(此帳戶會被搬移到 __Deleted Objects__ 容區內\)

等這個被刪除的 __王喬治__ 帳戶被複寫到網域控制站DC1，也就是等DC1內的 __王喬治__ 也被刪除\(預設是等15秒\)

在DC2上先執行 __非授權還原__ ，然後再執行 __授權還原__ ，它便會將被刪除的 __王喬治__ 帳戶復原

# 授權還原演練 - 1

先執行 __非授權還原__ ，然後再執行 __授權還原__

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_8.png)

* 等網域控制站之間的AD DS自動同步完成、或利用 __Active Directory__  __站台及服務__ 手動同步、或執行以下指令來手動同步：
  * __repadmin__  __  __  __/__  __syncall__  __ dc2\.sayms\.local  /e  /d  /A  /P__
* 若要針對整個AD DS資料庫執行 __授權還原__ 的話，請執行 __restore __  __database__ ；若要針對組織單位 __業務部__ 執行 __授權還原__ 的話：
  * __restore  __  __subtree__  __  OU=__  __業務部__  __\,DC=__  __sayms\,DC__  __=local__

---

/e:Enterprise(所有站台)、/d:distinguished name、/A:All(所有目錄分割區)、/P:Push(同步方向是將網域控制站dc2.sayms.local的異動資料傳送給其他網域控制站

# 授權還原演練 - 2

完成同步後，可利用 __Active Directory__  __管理中心__ 來驗證組織單位 __業務部__ 內的使用者帳戶王喬治已經被復原

也可以透過指令來驗證 __王喬治__ 帳戶的屬性版本號碼確實被增加了100\,000

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_9.png)

---

若是使用wbadmin程式，且要針對SYSVOL資料夾執行授權還原的話，請在執行非授權還原時，增加-authsysvol參數，例如：wbadmin  start  systemstaterecovery  –其他參數  –authsysvol

# AD DS資料庫的搬移與重整

* 資料庫與異動記錄檔的儲存地點預設是在% _systemroot_ %\\NTDS內，若硬碟儲存空間不足或為了提高運作效率的話，有可能需要將AD DS資料庫搬移到其他地點或重整
* 可重新啟動的AD DS
  * 可以選擇重新啟動電腦，以便進入 __目錄__  __服務修復__  __模式__ 來執行搬移AD DS資料庫、資料庫離線重整等維護工作
  * 重新啟動期間將造成所有服務會暫停，例如DHCP伺服器服務
  * __可__  __重新啟動的__  __AD __  __DS__
    * 只需將AD DS服務停止，不需要重新啟動電腦來進入 __目錄服務修復__  __模式__
    * 讓AD DS資料庫的維護工作更容易、更快完成，且其他服務也不會被中斷
    * 完成維護工作後再重新啟動AD DS服務即可

# 搬移AD DS資料庫檔案 - 1

必須至少是隸屬於Administrators群組的成員

開啟 __命令提示字元__ 或 __Windows PowerShell__ 視窗

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_10.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_11.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_12.png)

---

不需手動建立資料夾，因為Ntdsutil.exe會自動建立。若要事先建立此資料夾的話，請確認SYSTEM與Administrators對此資料夾擁有完全控制的權限
若要變更SYSVOL資料夾的儲存地點的話，建議方法為：移除AD DS、重新安裝AD DS、在安裝過程中指定新的儲存地點

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_13.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_14.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_15.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_16.png)

回到 __命令提示字元__ 下執行 __net  start  __  __ntds__ 指令，以便重新啟動AD DS服務

# 重整AD DS資料庫 - 1

* 將資料庫內的資料排列的更整齊，讓資料的讀取更快速，可以提升AD DS運作效率
* AD DS資料庫的重整分為
  * __線上重整__ ：網域控制站每隔12小時會透過 __垃圾__  __收集__  __程序__ 來重整AD DS資料庫。它也會將 __Deleted Objects__ 容區內已經超過180天的物件刪除。騰出的空間並不會還給作業系統，因此資料庫檔案的大小並不會減少，不過當您建立新物件時，該物件就會使用騰出的可用空間
  * __離線重整__ ：需在AD DS服務停止或 __目錄服務修復模式__ 內手動進行，它會建立一個全新的、整齊的資料庫檔案，並會將已刪除的物件所佔用空間還給作業系統
* 若網域控制站曾經兼具 __通用類別目錄伺服器__ 角色，但現在已經不再是 __通用類別目錄伺服器__ 的話，則經過 __離線重整__ 後，新的AD DS資料庫檔案會比原來的檔案小很多、可以騰出很多的硬碟空間給作業系統

執行 __net  stop  __  __ntds__ 指令

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_17.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_18.png)

將原資料庫檔案C:\\Windows\\NTDS\\ntds\.dit備份起來，以備不時之需

將重整後的新資料庫檔案C:\\NTDSTemp\\ntds\.dit複寫到C：\\Windows\\NTDS資料夾，並覆蓋原資料庫檔

將原異動記錄檔C:\\Windows\\NTDS\\\*\.log刪除

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_19.png)

* 回到 __命令提示字元__ 下執行 __net  start  __  __ntds__ 以便重新啟動AD DS服務
* 若無法啟動AD DS服務的話，請試著採用以下途徑來解決問題
  * 利用 __事件檢視器__ 來查看 __目錄服務__ 記錄檔，若有事件識別碼為1046或1168的事件記錄的話，請利用備份來復原AD DS
  * 再執行資料庫完整性檢查\(integrity\)，若檢查失敗的話，請將之前備份的資料庫檔案ntds\.dit複寫回原資料庫儲存地點，然後重複資料庫重整動作，若這個動作中的資料庫完整性檢查還是失敗的話，請執行語義資料庫分析工作\(semantic database analysis\)，若失敗的話，請執行修復資料庫的動作\(recover\)

# 重設「目錄服務修復模式」的系統管理員密碼

可以在一般模式下，利用 __ntdsutil__ 程式來重設 __目錄服務修復模式__ 的系統管理員密碼

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_20.png)

---

要被重設密碼的網域控制站，其AD DS服務必須啟動中

# 變更「可重新啟動的AD DS 」的登入設定 - 1

* 在AD DS服務停止的情況下，只要還有其他網域控制站在線上，則仍然可在這台AD DS服務已停止的網域控制站上利用網域帳戶來登入
* 但若沒有其他網域控制站在線上的話，可能會造成困擾，例如：
  * 您在網域控制站上利用網域系統管理員的身分登入
  * 將AD DS服務停止
  * 一段時間未操作此電腦，因而螢幕保護程式被啟動，且需輸入密碼才能解鎖
  * 但AD DS服務已停止，且沒有其他網域控制站在線上，因此無法驗證網域系統管理員身分，也就無法解開螢幕保護的鎖定
* 若有變更登入設定的話，就可在此時利用 __目錄服務修復__  __模式__ 的系統管理員來解除鎖定

* 變更登入設定的方法為：執行登錄編輯程式REGEDIT\.EXE，然後修改或新增以下的登錄值
* __HKEY\_LOCAL\_MACHINE\\System\\__  __CurrentControlSet__  __\\Control\\__
* __ __  __    __  __Lsa__  __\\__  __DSRMAdminLogonBehavior__
* __DSRMAdminLogonBehavior__ 的資料類型為REG\_DWORD
  * __0__ ：不可以登入。 __DSRM__  __系統管理員__ 只可登入到 __目錄服務修復模式__ \(預設值\)
  * __1__ ： __DSRM__  __系統管理員__ 可以在AD DS服務停止的情況下登入，不過 __DSRM__  __系統管理員__ 不受密碼原則設定的約束。在網域中只有一台網域控制站的情況之下，或某台網域控制站是在一個隔離的網路等狀況之下，此時或許您比較希望能夠將此參數改為這個設定值。
  * __2__ ：不論AD DS服務是否啟動、不論是否在 __目錄服務修復模式__ 下，都可以使用 __DSRM__  __系統管理員__ 來登入。不建議您採用此方式，因為 __DSRM__  __系統管理員__ 不受密碼原則設定的約束

# Active Directory資源回收筒

舊版Windows系統中，系統管理員較易不小心將AD DS物件刪除，因而造成不少困擾，尤其是誤刪組織單位的話，其內所有物件都會不見

較新Windows系統中針對此事稍作改良，例如可以在新增使用者與群組帳戶等物件時，勾選 __保護以防止被意外__  __刪除__

也支援 __Active Directory__  __資源回收__  __筒__ ，它讓您不需要進入 __目錄服務修復模式__ ，就可以快速救回被刪除的物件

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_21.png)

# 啟用Active Directory資源回收筒 - 1

* 樹系與網域功能等級需為Windows Server 2008 R2\(含\)以上的等級
  * 樹系中所有網域控制站都需為Windows Server 2008 R2\(含\)以上
* 一旦啟用 __Active Directory__  __資源回收筒__ 後，就無法再停用

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_22.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_23.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_24.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_25.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_26.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_27.png)

![](WS2016AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA245-Ch11-AD%20DS%E7%9A%84%E7%B6%AD%E8%AD%B7_28.png)

