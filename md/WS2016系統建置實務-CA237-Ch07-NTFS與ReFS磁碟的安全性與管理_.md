第 7 章  NTFS與ReFS磁碟的安全性與管理

7\-1 NTFS與ReFS權限的種類

7\-2 使用者的有效權限

7\-3 權限的設定

7\-4 檔案與資料夾的擁有權

7\-5 檔案拷貝或搬移後的權限變化

7\-6 檔案的壓縮

7\-7 加密檔案系統

7\-8 BitLocker磁碟機加密

7\-9 磁碟重組與檢查磁碟錯誤

7\-10 磁碟配額

7\-1 NTFS與ReFS權限的種類

# 基本檔案權限的種類

讀取\(Read\)

寫入\(Write\)

讀取和執行\(Read&Execute\)

修改\(Modify\)

完全控制\(Full Control\)

# 基本資料夾權限的種類

讀取\(Read\)

寫入\(Write\)

列出資料夾內容\(List Folder Contents\)

讀取和執行\(Read&Execute\)

修改\(Modify\)

完全控制\(Full Control\)

# 權限是可以被繼承的

* 資料夾的權限設定，會被這個資料夾之下的子資料夾與檔案來繼承
* 在設定資料夾權限時
  * 可以讓子資料夾與檔案都來繼承權限
  * 可以只單獨讓子資料夾或檔案來繼承
  * 不讓子資料夾與檔案來繼承
* 子資料夾或檔案也可以被設定為不要繼承父資料夾的權限，此時它們的權限將是以您直接設定的權限為權限

# 7-2 使用者的有效權限

權限有累加性

「拒絕」權限的優先權較高

\* 繼承來的權限，其優先權比直接設定的權限低

| 使用者或群組 | 權限 |
| :-: | :-: |
| 使用者A | 寫入 |
| 群組 業務部 | 讀取 |
| 群組 經理 | 讀取和執行 |
| 使用者A最後的有效權限為 寫入 + 讀取 + 執行 |  |

| 使用者或群組 | 權限 |
| :-: | :-: |
| 使用者A | 讀取 |
| 群組 業務部 | 讀取被拒絕 |
| 群組 經理 | 修改 |
| 使用者A的讀取權限為 拒絕 |  |

---

例如使用者A對甲資料夾的寫入權限為拒絕，並且讓檔案來繼承此權限的話，則使用者A對此檔案的寫入權限也會被拒絕，但若您另外直接將使用者A對此檔案的寫入權限設定為允許的話，此時因為它的優先權較高，故使用者A對此檔案仍然擁有寫入的權限

# 7-3 權限的設定

每一個新NTFS或ReFS磁碟都有預設權限，如圖所示為C:磁碟\(NTFS\)的預設權限，其中有部份的權限會被其下的子資料夾或檔案來繼承

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_0.png)

# 指派權限

* 指派檔案的權限
  * 對著檔案按右鍵內容安全性
  * 可不繼承父資料夾的權限
* 指派資料夾的權限
  * 對著資料夾按右鍵內容安全性
* 特殊權限的指派
  * 進階權限設定

# 特殊權限

周遊資料夾/執行檔案

列出資料夾/讀取資料

讀取屬性

讀取擴充屬性

建立檔案/寫入資料

建立資料夾/附加資料

寫入屬性

寫入擴充屬性

刪除子資料夾及檔案：讓使用者可以刪除此資料夾內的子資料夾與檔案，即使使用者對這個子資料夾或檔案沒有 __刪除__ 的權限也可以將其刪除

刪除

讀取權限

變更權限

取得擁有權

# 使用者的有效權限

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_1.png)

加總使用者本身、全域群組、萬用群組、網域本機群組、本機群組、 Everyone的權限

不會將某些特殊群組的權限計算進來，例如Network與Interactive群組

# 7-4 檔案與資料夾的擁有權

* 建立檔案或資料夾的使用者，就是擁有者
* 擁有者可以變更其權限，不論其目前是否有存取權限
* 使用者若具備以下的條件之一，便可以取得擁有權
  * 具備“取得檔案或其它物件的擁有權”權利，例如Administrators
  * 對該檔案或資料夾擁有“取得擁有權”  的特別權限
  * 具備還原檔案及目錄的權利

# 取得擁有權後

* 便具備變更其權限的能力，但並不會影響此使用者與原擁有者的既有權限
  * 需在群組原則中的 __使用者帳戶控制__  __: __  __所有系統管理員均以管理員核准模式__  __執行__ 被停用，才會直接出現前景畫面，否則會先要求輸入系統管理員的密碼

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_2.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_3.png)

# 7-5 檔案拷貝或搬移後的權限變化

* 檔案搬移
  * 需對來源檔案或資料夾具備 __修改__ 的權限
  * 需對目的資料夾具備有 __寫入__ 的權限
* 若將檔案搬移或拷貝到FAT、FAT32或exFAT磁碟內，則原有權限設定都將被移除

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_4.png)

---

將檔案或資料夾拷貝或搬移到USB隨身碟後，其權限變化視檔案系統而定。USB隨身碟可被格式化成FAT、FAT32、exFAT或NTFS檔案系統 (抽取式儲存媒體不支援ReFS)

# 7-6 檔案的壓縮

* NTFS壓縮
  * 僅NTFS磁碟支援
  * ReFS、exFAT、FAT32與FAT都不支援
* 壓縮的\(zipped\)資料夾
  * NTFS、ReFS、exFAT、FAT32與FAT磁碟皆支援
  * 與WINZIP/WINRAR等程式相容

---

隨身碟不支援NTFS壓縮


# NTFS壓縮

* 對著檔案按右鍵內容按 __進階__ 鈕
* 讀取壓縮檔案時
  * 系統會將檔案由磁碟內讀出、自動解壓縮
  * 磁碟內的檔案仍然是處於壓縮狀態
* 寫資料到壓縮檔時
  * 自動壓縮後再寫入磁碟內

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_5.png)

若欲將壓縮或加密的檔案以不同的顏色來顯示的話，請【開啟 __檔案總管__ 點擊上方檢視點擊右方選項圖示勾選檢視標籤下的使用色彩顯示加密或壓縮的NTFS檔案

---

也可以利用COMPACT.EXE 程式

# 檔案拷貝或搬移時壓縮屬性的變化

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_6.png)

# 壓縮的(zipped)資料夾

被拷貝到此資料夾的檔案都會被自動壓縮

不需自行解壓縮，可直接讀取 __壓縮的__  __\(zipped\)__  __資料夾__ 內的檔案，甚至可以直接執行其內的程式

資料夾名稱的副檔名為\.zip，它可以被WinZip、WinRAR等檔案壓縮工具程式來解壓縮，也可以被拷貝或搬移到其他的任何的磁碟或其它電腦

附檔名為\.zip，預設會被隱藏

若已安裝WINZIP或WINRAR等軟體的話，系統會改由WINZIP或WINRAR等來開啟此資料夾

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_7.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_8.png)

# 7-7 加密檔案系統

* 僅NTFS支援
* 僅當初將其加密的使用者或被授權的使用者能夠讀取
  * 還需具備NTFS權限
* 若將檔案複製或搬移到非NTFS磁碟內，此新檔案會被解密
* 檔案壓縮與加密無法並存

---

隨身碟不支援加密(不論檔案系統為何)

# 將檔案與資料夾加密

對著資料夾按右鍵內容按 __進階__ 鈕勾選 __加密內容，保護資料__

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_9.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_10.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_11.png)

# 加密屬性的變化

* 讀取加密檔案時
  * 系統將檔案由磁碟內讀出、自動解密
  * 磁碟內的檔案仍然是處於加密的狀態
* 寫資料到加密檔案時
  * 自動加密後再寫入磁碟內
* 未加密檔案被搬移或拷貝到加密資料夾後
  * 該檔案會被自動加密
* 加密檔案被搬移或拷貝到非加密資料夾\(NTFS\)時
  * 該檔案仍然會保持其加密的狀態
* 利用EFS加密的檔案，只有儲存在硬碟內才會被加密，透過網路來傳送時沒有加密
  * 可透過IPSec或WebDev的方式來加密

# 授權其他使用者可以讀取加密的檔案 - 1

* 僅加密者有權讀取，但可授與其他使用者權限
* 使用者必須擁有 __EFS__  __憑證__ 才可以被授權
  * 使用者在第1次執行加密動作後，就會被自動賦予 __EFS__  __憑證__
* 具備 __修復憑證__ 的使用者也可以存取被加密的檔案
  * 預設僅網域Administrator擁有此憑證
  * 可以透過群組原則或本機原則來將 __修復憑證__ 指派給其他使用者

---

以本機原則來說：開始Windows系統管理工具本機安全性原則公開金鑰原則對著加密檔案系統按右鍵新增資料修復代理

* 先請即將被授權者對任一檔案執行加密的動作
  * 以便擁有 __EFS__  __憑證__
* 對著加密的檔案按右鍵內容按 __進階__ 鈕按詳細資料鈕

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_12.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_13.png)

# 備份EFS憑證

* __EFS__  __憑證__ 遺失或損毀的話，將無法讀取加密檔
* 執行CERTMGR\.MSC
  * 需匯出私密金鑰並設定密碼
  * 將此憑證檔儲存到安全的地方
  * 若您擁有多個 __EFS__  __憑證__ 的話，請全部匯出

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_14.png)

# 7-8 BitLocker磁碟機加密

* BitLocker：不需擔心磁碟失竊、資料外洩
  * 可將安裝Windows Server 2016的整個磁碟加密
  * 無法被拿到另一台電腦啟動
  * 也無法在另一台電腦當作資料碟來讀取
* BitLocker to Go：不需擔心抽取式磁碟失竊、資料外洩
  * 用來將抽取式磁碟\(例如USB隨身碟\)加密

# BitLocker的需求

* 至少需兩個磁碟分割區
  * 一個用來安裝Windows Server 2016\(一般為C：\)的NTFS磁碟分割區，可選擇是否要將其加密
  * 一個用來啟動電腦
    * 需被設定為 __使用中__ \(active\)
    * 不可以被加密
    * 檔案系統
      * 傳統BIOS：NTFS
      * UEFI BIOS ：FAT32

---

若硬碟內還有其他磁碟分割區的話，BitLocker也可以將它們加密

# 作業系統磁碟的保護功能

* 可信賴平台模組\(TPM\)
  * TPM是一個微晶片，有些電腦擁有此晶片
  * BitLocker會將解密金鑰儲存到此晶片內
  * 電腦啟動時會到TPM讀取金鑰，並利用它將作業系統磁碟解密與啟動作業系統
  * 符合TCG規範的傳統BIOS或UEFI BIOS，需啟用對TPM的支援
* USB隨身碟
  * 會將解密金鑰儲存到USB設備內
  * 每次啟動電腦時都必須插入USB設備
  * 電腦啟動時會到USB設備讀取金鑰，並利用它將作業系統磁碟解密與啟動作業系統
  * BIOS需啟用對USB設備的支援
* 密碼
  * 使用者在電腦啟動時需輸入所設定的密碼來解鎖

# 固定式或抽取式資料磁碟的保護功能

* __密碼__ ：使用者要存取該資料磁碟時，需輸入密碼來解鎖
* __智慧卡__ ：使用者要存取該資料磁碟時，需插入智慧卡、輸入PIN號碼來解鎖
* __自動解__  __鎖__
  * 固定式資料磁碟： 只要作業系統磁碟有BitLocker加密保護，就可以設定自動將此資料磁碟解鎖，以後系統啟動時，此資料磁碟就會自動解鎖。
  * 針對抽取式資料磁碟來說\(作業系統磁碟不需BitLocker加密保護\)，在先使用密碼或智慧卡解鎖後，就可以設定為以後自動解鎖

# BitLocker實例演練(以作業系統加密+密碼解鎖為例)

準備所需的磁碟分割區

安裝BitLocker磁碟機加密

允許在無TPM環境下來使用BitLocker

啟用BitLocker磁碟機加密

暫停、關閉BitLocker與金鑰管理

# 準備磁碟分割區

* 安裝Windows Server 2016時：
  * 傳統BIOS模式：會自動建立兩個磁碟分割區
  * UEFI模式：會自動建立三或四個磁碟分割區
* 其中一個被設定為用來啟動電腦、另一個用來安裝Windows Server 2016，此環境已經可以用來支援BitLocker功能

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_15.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_16.png)

# 安裝「BitLocker磁碟機加密」

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_17.png)

---

在網域環境下，若用戶端電腦的作業系統磁碟被BitLocker保護、且其BIOS為新版UEFI BIOS(內含DHCP驅動程式)、配備TPM 1.2(或新版)+PIN，則當此電腦啟動時，可以透過一台安裝Windows部署服務的伺服器所提供的金鑰來遠端解鎖此用戶端電腦。這台Windows部署服務伺服器需安裝圖中的BitLocker網路解除鎖定功能，並安裝適當憑證

# 允許在無TPM環境下來使用BitLocker

* 預設只支援TPM方式的BitLocker
* 讓BitLocker支援密碼解鎖方式
  * 執行GPEDIT\.MSC後透過 __電腦設定__ 來設定

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_18.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_19.png)

# 啟用BitLocker磁碟機加密 - 1

控制台系統及安全性BitLocker磁碟加密

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_20.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_21.png)

\*若要將固定式或抽取式資料碟\(例如USB隨身碟\)加密的話，請透過圖中的 __固定式資料__  __磁碟機__  __  __ 或 __抽取式__  __資料__  __磁碟機 __  __\- __  __BitLocker__  __ To Go__ 處的 __開啟__  __BitLocker__ 來設定

---


儲存修復金鑰的USB隨身碟建議用另外一個隨身碟，不要用儲存解密金鑰的隨身碟，以免隨身碟遺失時，解密金鑰與修復金鑰同時遺失
若選擇儲存到資料夾的話，該資料夾不可位於Windows Server 2016作業系統磁碟內

* TPM電腦啟動時若啟動環境有異動的話\(例如BIOS更新等\)，BitLocker不會將作業系統磁碟解鎖，此時可利用 __修復金鑰__ 來將磁碟解鎖與啟動系統，因此您需在首次啟用BitLocker時建立 __修復金__  __鑰__
* 使用USB快閃磁碟來儲存解鎖金鑰的電腦雖然不會檢查啟動環境是否有異動，不過還是需要建立 __修復金鑰__ ，以免USB快閃磁碟故障、遺失
  * 建議用另外一個USB快閃磁碟，以免此USB快閃磁碟遺失時，解鎖金鑰與修復金鑰同時遺失

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_22.png)

重新啟動、輸入解鎖密碼、登入後，可以從工作列圖示得知BitLocker開始將作業系統磁碟加密

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_23.png)

每次啟動電腦時，都需輸入解鎖密碼，才可以啟動Windows Server 2016；密碼忘記時，可輸入 __修復金鑰__ 來解鎖

---

將安裝磁碟加密需花費一段蠻長的時間


# 暫停與繼續BitLocker

* 暫停BitLocker
  * 若需要更新電腦的BIOS韌體、硬體或作業系統的話
  * 暫停後，下次重新啟動電腦時就不需要輸入密碼，不過電腦啟動完成後，BitLocker就會自動重新啟用
* 繼續BitLocker
  * 由於磁碟仍然是保持加密狀態，因此不需要再經過冗長的加密過程

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_24.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_25.png)

# 關閉BitLocker

* 磁碟會被解密
* 若要重新啟用BitLocker
  * 需要建立新的解密金鑰與修復金鑰
  * 需要花費很長時間來重新將磁碟加密

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_26.png)

---

若要複製修復金鑰或解密金鑰的話，請點擊圖中的管理BitLocker

# 7-9 磁碟重組與檢查磁碟錯誤

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_27.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_28.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_29.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_30.png)

---

磁碟用了一段時間後，儲存在磁碟內的檔案可能會零零散散的分佈在磁碟內，因而影響到磁碟的存取效率

# 7-10 磁碟配額

用來追蹤、限制使用者在NTFS磁碟內的儲存空間

針對單一使用者來控制與追蹤

僅NTFS磁碟支援，ReFS、exFAT、FAT32與FAT磁碟不支援

以檔案與資料夾的擁有權來計算

不考慮檔案壓縮的因素

每一個NTFS磁碟的磁碟配額獨立計算

系統管理員不受限制

# 磁碟配額的設定

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_31.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_32.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_33.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_34.png)

# 監控每一個使用者的磁碟配額使用情況

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_35.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_36.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch07-NTFS%E8%88%87ReFS%E7%A3%81%E7%A2%9F%E7%9A%84%E5%AE%89%E5%85%A8%E6%80%A7%E8%88%87%E7%AE%A1%E7%90%86_37.png)

