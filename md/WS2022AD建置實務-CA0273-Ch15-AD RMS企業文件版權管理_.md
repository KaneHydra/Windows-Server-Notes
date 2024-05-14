第 15 章 AD RMS企業文件版權管理

15\-1 AD RMS概觀

15\-2 AD RMS實例演練

# 15-1 AD RMS概觀

* 彌補NTFS\(與ReFS\)權限功能不足之處
  * 擁有NTFS讀取權限就可以複製，可輕易將機密文件帶離
  * Active Directory Rights Management Services可彌補NTFS缺點、保護企業內部的機密文件與智慧財產
* 文件擁有者透過AD RMS\-enabled __應用程式__ 可將文件設定為版權保護文件，並授予其他使用者讀取、複製或列印文件等權限
  * 僅擁有讀取權限的使用者無法複製文件內容、也無法列印文件
* 寄送郵件者也可以限制收件者轉送此郵件
* 版權保護文件內儲存著保護資訊，不論文件被搬移、複製到何處，這些保護資訊都仍然存在文件內，因此可以確保文件不會被未經授權的使用者存取

# 基本AD RMS環境的架構圖

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_0.png)

# 基本AD RMS環境的需求

* 網域控制站
  * AD RMS需Active Directory網域
* AD RMS伺服器
  * 負責憑證與授權的發放
  * 可架設多台AD RMS伺服器\(容錯與負載平衡\)，第1台稱為AD RMS __根叢集伺服器__
  * 需架設IIS網站:用戶端需透過HTTP\(或HTTPS\)來與AD RMS伺服器溝通
* 資料庫伺服器
  * 儲存AD RMS的設定與原則等資訊
  * 可使用Microsoft SQL Server或AD RMS伺服器的內建資料庫
    * 此時只能夠架設一台AD RMS伺服器
* 執行AD RMS\-enabled __應用程式__ 的用戶端
  * 利用它來建立、編輯與將文件設定為受版權保護的文件，例如Microsoft Office  Word 2021

---

用戶端需憑證與授權才可以進行文件版權保護的工作、存取版權保護文件


# AD RMS如何運作

文件擁有者第一次執行保護文件工作時，會從AD RMS伺服器取得憑證，便可以執行保護文件的工作

文件擁有者利用AD RMS\-enabled __應用程式__ 建立文件，並且執行保護文件的步驟，也就是設定此文件的使用權限與使用條件，同時該應用程式會將此文件加密與建立 __發行授權__ ， __發行授權__ 內包含著文件的使用權限、使用條件與解密金鑰

文件擁有者將受保護的文件\(內含 __發行授權__ \)儲存到可供文件接收者存取的地方，或是將它直接傳送給文件接收者

文件接收者利用AD RMS\-enabled __應用程式__ 來開啟文件時，會向AD RMS伺服器送出索取 __使用授權__ 的要求\(此要求內包含 __發行授權__ \)

AD RMS伺服器透過 __發行授權__ 內的資訊來確認文件接收者有權存取此文件後，會建立使用者所要求的 __使用授權__ \(內含使用權限、使用條件與解密金鑰\)，然後將 __使用授權__ 傳給文件接收者

文件接收者的AD RMS\-enabled __應用程式__ 收到 __使用授權__ 後，會利用 __使用授權__ 內的解密金鑰來將受保護的文件解密與存取該文件

# 15-2 AD RMS實例演練圖

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_1.png)

---

虛擬機器利用Sysprep.exe來變更其SID

# 準備好電腦

* 安裝好圖中每一台電腦所需的作業系統
* 若使用Hyper\-V 虛擬機器
  * 分別在3個電腦內執行Sysprep\.exe來變更SID等
* 設定每一台電腦的網路卡IP位址、子網路遮罩、慣用DNS伺服器
* 將3台電腦的電腦名稱分別變更為DC、ADRMS與Win11PC1後重新啟動
* 暫時將每一台電腦的 __Windows __  __Defender __  __防火牆__ 關閉
* 透過PING來測試電腦之間是否可以正常溝通
* 可重新開啟每一台電腦的 __Windows __  __Defender __  __防火牆__
* 利用將伺服器DC升級為網域控制站的方式來建立網域sayms\.local
* 將伺服器ADRMS與Win11PC1加入網域sayms\.local

# 建立使用者帳戶

* 建立測試用的使用者帳戶與啟動AD RMS服務的帳戶
  * George、Mary、ADRMSSRVC
  * 密碼永久有效
  * 在AD資料庫內替George與Mary設定電子郵件地址

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_2.png)

# 安裝AD RMS

* 利用網域Administrator登入
  * 執行安裝工作的使用者需隸屬於本機Administrators與網域Enterprise Admins
* 透過 __伺服器管理__ 的 __新增角色及功能__ 來安裝Active Directory Rights Management Services
* 目前登入的使用者帳戶\(網域Administrator\)會被加入到本機AD RMS Enterprise __系統管理員__ 群組內，它讓使用者有權利來管理AD RMS
  * 使用者需先登出、再重新登入以便取得新的 __存取權杖__ 後才有效

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_3.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_4.png)

---

使用內建資料庫只能夠架設一台AD RMS伺服器、AD RMS會利用叢集金鑰來簽署所發放的憑證與授權、加入此叢集的其他AD RMS伺服器會共用這個SLC憑證
用來將AD RMS SCP登錄到Active Directory的使用者帳戶必須是隸屬於網域群組Enterprise Admins


# 建立儲存版權保護文件的共用資料夾

* 將資料夾設定為共用資料夾
  * 假設將其建立電腦DC上 、共用名稱為public
* 開放適當權限給使用者

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_5.png)

# 測試AD RMS的功能 - 1

安裝Microsoft office Word 2021

利用文件擁有者身份登入

將AD RMS叢集網站https://adrms\.sayms\.local加入近端內部網路

建立WORD文件、限制存取、將檔案儲存到共用資料夾

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_6.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_7.png)

http://adrms\.sayms\.local未被加入到近端內部網路

---

可透過檢視憑證鈕來執行信任的步驟

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_8.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_9.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_10.png)

* 利用文件接收者身份登入
* 將AD RMS叢集網站https://adrms\.sayms\.local加入近端內部網路
* 利用WORD開啟版權保護文件
  * 僅能閱讀此文件
  * 無法另存新檔
  * 無法列印文件\(包含透過按PrtScr鍵或Alt \+ PrtScr鍵\)
  * 無法選取 __複製__ 與 __剪下__ 文件內容

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_11.png)

# 限制郵件轉寄

透過Microsoft Office Outlook可以限制收件者轉寄郵件

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_12.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch15-AD%20RMS%E4%BC%81%E6%A5%AD%E6%96%87%E4%BB%B6%E7%89%88%E6%AC%8A%E7%AE%A1%E7%90%86_13.png)

---

還需要有電子郵件伺服器（例如Microsoft Exchange Server）、替寄件者與收件者建立電子郵件信箱、安裝Outlook

