第 12 章 RADIUS伺服器的架設

12\-1 RADIUS概觀

12\-2 安裝網路原則伺服器（NPS）

12\-3 RADIUS伺服器與用戶端的設定

12\-4 RADIUS代理伺服器的設定

# RADIUS概觀

* RADIUS\(Remote Authentication Dial\-In User Service\)
  * 用戶端/伺服器通訊協定
  * 讓RADIUS用戶端可以將驗證使用者身分\(authentication\)、授權\(authorization\)與帳戶處理\(accounting\)等工作，轉給RADIUS伺服器來執行
  * 或將上述工作轉給RADIUS代理伺服器，再由它轉給RADIUS伺服器來執行
* Windows Server 2016透過 __網路原則伺服器__ \(NPS\)來提供RADIUS伺服器與RADIUS代理伺服器的服務

# RADIUS伺服器

* NPS可以讓Windows Server 2016電腦來扮演RADIUS伺服器的角色
* RADIUS用戶端:
  * 一般的遠端存取伺服器\(利用數據機連接用戶端\)
  * VPN伺服器
  * 無線基地台\(AP\)
* RADIUS伺服器替RADIUS用戶端來執行驗證使用者身分、授權與帳戶處理等工作

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_0.png)

# RADIUS伺服器的驗證流程

* 遠端存取伺服器、VPN伺服器或無線基地台等存取伺服器接收來自用戶端的連線要求
* 存取伺服器會轉而請求RADIUS伺服器來執行驗證等工作
* RADIUS伺服器檢查使用者名稱與密碼是否正確\(authentication\)，並且透過以下兩處的設定來決定使用者是否被允許來連線\(authorization\)
  * 使用者帳戶內容\( __撥入__ 標籤\)
  * 網路原則

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_1.png)

4\. 若使用者被允許來連線，RADIUS伺服     器會通知存取伺服器，再由存取伺服     器讓用戶端來連線。同時存取伺服器     也會通知RADIUS伺服器將此次的連線     要求記錄起來

# 使用者帳戶

* 使用者可透過以下帳戶來連線
  * RADIUS伺服器的本機安全性資料庫
  * 網域的Active Directory資料庫
    * RADIUS伺服器需為網域的成員
    * 使用者帳戶可以是所屬網域內的帳戶，或是有雙向信任關係的其他網域內的帳戶
* 透過RADIUS伺服器來集中執行驗證、授權與帳戶處理工作的好處
  * 只需要維護位於RADIUS伺服器內的網路原則與記錄檔即可，可減輕管理負擔

---

若未將驗證、授權與帳戶處理的工作轉給RADIUS伺服器的話，則每一台遠端存取伺服器或VPN伺服器必須自己執行這些工作，因此每一台遠端存取伺服器或VPN伺服器都需要有自己的網路原則，如此將增加維護這些資料的負擔

# RADIUS代理伺服器

RADIUS代理伺服器可以將由RADIUS用戶端所送來的驗證身分、授權與帳戶處理等要求轉送給其他RADIUS伺服器來執行

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_2.png)

# RADIUS代理伺服器的使用場合

* 若使用者帳戶是位於未建立信任關係的網域內、只有單向信任關係的網域內、其他網域樹系內，則RADIUS伺服器將無法讀取到使用者帳戶的資料，因而無法驗證使用者身分：
  * 此時可以透過RADIUS代理伺服器來將驗證、授權與帳戶處理工作，轉送給可以讀取使用者帳戶資料的RADIUS伺服器來執行
  * 這些RADIUS伺服器可能分別隸屬於不同的網域、不同的網域樹系，甚至是其他非微軟系統的RADIUS伺服器
* 分散RADIUS伺服器的負擔
  * 如果有大量的用戶端連線要求的話，則可以透過RADIUS代理伺服器將這些連線要求轉送到不同的RADIUS伺服器，以便加快處理的效率

# RADIUS代理伺服器的驗證流程

遠端存取伺服器、VPN伺服器或無線基地台等存取伺服器接收來自用戶端的連線要求

存取伺服器會轉而請求RADIUS代理伺服器來執行驗證、授權與帳戶處理的工作

RADIUS代理伺服器會再轉而請求RADIUS伺服器來執行驗證、授權與帳戶處理的工作

RADIUS伺服器會驗證使用者的身分與決定是否允許用者來連線

若使用者被允許來連線，則RADIUS伺服器會通知RADIUS代理伺服器，然後再由RADIUS代理伺服器轉通知存取伺服器，再由存取伺服器讓用戶端來連線。同時存取伺服器也會通知RADIUS代理伺服器將這次的連線要求記錄起來

# RADIUS伺服器實例演練圖

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_3.png)

RADIUS1為Windows Server 2016 RADIUS伺服器、網域成員伺服器

VPNS1為VPN伺服器、RADIUS用戶端、獨立伺服器\(無法直接將網域使用者帳戶與密碼送到網域控制站來檢查，故採用RADIUS\)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_4.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_5.png)

在RADIUS1安裝網路原則伺服器\(NPS\)

---

若要管理其他RADIUS伺服器的話：【按鍵切換到開始選單執行輸入MMC後按確定鈕檔案功能表新增/移除嵌入式管理單元從清單中點選網路原則伺服器新增選擇另一台電腦、輸入電腦名稱或IP位址…

# 登錄網路原則伺服器 - 1

* 若NPS隸屬於AD網域的話，則需將NPS登錄到AD資料庫，也就是將NPS加入到網域的 __RAS and IAS Servers__ 群組內
  * 如此NPS便可以向網域控制站詢問使用者帳戶的相關資料，來判斷使用者名稱與密碼是否正確、決定使用者是否被允許來連線
* 如果要讓NPS讀取其他網域內的使用者帳戶資料的話，則需要將NPS登錄到其他網域的AD資料庫，也就是將NPS加入到其他網域的 __RAS and IAS Servers__ 群組內

到NPS電腦上利用網域系統管理員的身分登入

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_6.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_7.png)

* 直接將NPS電腦帳戶加入到 __RAS and IAS Servers__ 群組
  * 執行 __Active __  __Directory__  __使用者和電腦__ 或 __Active Directory__  __管理__  __中心__ ：將NPS電腦\(RADISU1\)加入到 __RAS and IAS Servers__ 群組\(位於 __Users__ 容區\)

# RADIUS伺服器與用戶端的設定

不論NPS是扮演RADIUS伺服器或RADIUS代理伺服器的角色，都必須指定其RADIUS用戶端，它們只接受這些用戶端所傳來的連線要求

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_8.png)

# RADIUS伺服器的設定 – 1

* NPS預設是扮演RADIUS伺服器角色
  * 以下步驟用來指定此RADIUS伺服器的RADIUS用戶端

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_9.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_10.png)

若輸入NetBIOS電腦名稱的話，請將RADIUS用戶端\(VPNS1\)的 __Windows__  __防火牆__ 關閉或例外開放 __檔案及印表機共用__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_11.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_12.png)

可選擇Microsoft或RADIUS Standard

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_13.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_14.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_15.png)

# RADIUS用戶端的設定

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_16.png)

* RADIUS用戶端
  * 遠端存取伺服器、VPN伺服器或無線基地台
  * 一般的用戶端電腦端並不是RADIUS用戶端
* 需在RADIUS用戶端\(例如VPN伺服器\)上設定將其用戶端\(例如VPN用戶端\)所傳來的連線要求轉送給RADIUS伺服器

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_17.png)

---

帳戶處理（accounting）的工作轉給RADIUS伺服器來執行，也就是讓RADIUS伺服器來記錄每一個連線的情形，例如每一個被接受的連線、被拒絕的連線等驗證記錄，還有登入/登出等用來記帳的記錄等

# 其他設定

在Active Directory資料庫開放使用者可以來連接VPN伺服器

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_18.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_19.png)

在用戶端電腦VPNC1改用網域使用者帳戶來連線

# RADIUS代理伺服器的設定

* 當RADIUS用戶端將連線要求轉送給NPS時，NPS是要扮演RADIUS伺服器、還是RADIUS代理伺服器的角色呢?
  * 透過 __連線要求原則__ \(connection request policies\)來決定
  * NPS安裝完成後，系統預設是將它設為RADIUS伺服器

# 連線要求原則

__連線要求原則__ 與 __網路原則__ 類似，只要使用者的連線要求滿足所定義的條件，就會以 __連線要求原則__ 內的設定，來決定讓NPS自行驗證此連線要求\(此時NPS是RADIUS伺服器\)，還是要將其轉送給另外一台RADIUS伺服器\(此時NPS是RADIUS 代理伺服器\)

內建一個 __連線要求原則__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_20.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_21.png)

# 內建連線要求原則的設定

* 驗證這個伺服器上的要求
  * 將此NPS當作是RADIUS伺服器\(預設值\)
* 轉送要求到下列遠端RADIUS伺服器群組進行驗證
  * 讓此NPS扮演RADIUS代理伺服器的角色
  * 會將驗證要求轉送到該群組中的RADIUS伺服器。需先建立RADIUS伺服器群組後才可以選擇此選項
* 不確認認證即接受使用者

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_22.png)

# 建立遠端RADIUS伺服器群組

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_23.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_24.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_25.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_26.png)

# 修改RADIUS伺服器群組的設定

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_27.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_28.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_29.png)

RADIUS代理伺服器將驗證等要求轉送給優先順序較高的RADIUS伺服器的頻率，會比優先順序較低的RADIUS伺服器來得頻繁。數字為1表示優先順序最高。若優先順序相同，則權數越高，頻率越高

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch12-RADIUS%E4%BC%BA%E6%9C%8D%E5%99%A8%E7%9A%84%E6%9E%B6%E8%A8%AD_30.png)

