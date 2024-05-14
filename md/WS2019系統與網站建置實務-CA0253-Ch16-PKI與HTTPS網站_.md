# PKI概觀

* PKI的功能
  * 將傳送的資料加密\(encryption\)
  * 驗證資料是否由寄件者所傳來\(authentication\)
  * 確認資料的完整性\(integrity\)
* 需要一組金鑰
  * 公開金鑰\(public key\)：可以公開給其他的使用者
  * 私密金鑰\(private key\)：使用者私有、儲存在使用者電腦內，只有該使用者能夠存取
* 需要向 __憑證授權單位__ \(CA\)申請憑證\(certificate\)來擁有與使用這一組金鑰

# 公開金鑰加密法

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_0.png)

* 資料被加密後，必須經過解密才讀得到資料的內容
* 公開金鑰加密法
  * 使用一組金鑰:公開金鑰與私密金鑰
  * 公開金鑰用來加密、私密金鑰用來解密
  * 又稱為 __非對稱式__ \(asymmetric\)加密法
* 秘密金鑰加密法
  * 又稱為 __對稱式__ \(symmetric\)加密法
  * 加密、解密都是使用同一個金鑰

# 公開金鑰驗證法

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_1.png)

* 收件者在收到經過 __數位__  __簽章__ 的資料時
  * 可透過數位簽章來驗證此資料是否確實是由寄件者本人所寄出
  * 可檢查資料在傳送的過程中是否被動過手腳

# 數位簽章的運作程序

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_2.png)

# https網站安全連線

* 需替網站向 __憑證授權單位__ 申請SSL憑證
  * 憑證內包含了公開金鑰、憑證有效期限、發放此憑證的CA、CA的數位簽章等
* 網站擁有SSL憑證之後
  * 用戶端與網站間可以透過SSL安全連線\(https\)來溝通
  * https://www\.sayms\.local/

# 如何建立SSL安全連線

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_3.png)

# 「憑證授權單位(CA)」概觀

* 負責發放憑證
* 電子郵件保護或https網站安全連線，都必須擁有憑證
* 公開金鑰與私密金鑰的產生
  * 申請憑證時會自動建立公開與私密金鑰：私密金鑰會被儲存到使用者電腦的登錄中、申請資料與公開金鑰會一併被傳送到CA
  * CA利用自己的私密金鑰將要發放的憑證加以簽章後發放
  * 使用者收到憑證後，將憑證安裝到他的電腦
* 憑證內包含了憑證的發放對象\(使用者或電腦\)、憑證有效期限、發放此憑證的CA與CA的數位簽章等資料，還有申請者的姓名、地址、電子郵件信箱、公開金鑰等

---

若有安裝讀卡機的話，可以利用智慧卡來登入，不過也需要透過類似程序來申請憑證，CSP會將私密金鑰儲存到智慧卡內

# CA的信任

* 電子郵件
  * 收件者的電腦應該要信任由CA所發放的憑證
* 連接https網站
  * 用戶端電腦應該信任發放SSL憑證給此網站的CA
* Windows系統預設已自動信任知名CA

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_4.png)

---

按Windows + R鍵輸入control後按Enter鍵網路和網際網路網際網路選項內容標籤憑證鈕

# 申請憑證

* 可向知名CA申請憑證
  * 適合於對外界溝通來使用
* 自行架設CA
  * 適合於企業內各分公司之間、與事業合作夥伴之間、與供應商之間、與客戶之間來使用
  * 安裝Windows Server 2019的Active Directory憑證服務\(AD CS\)
  * 需讓電腦來信任此CA
  * CA的種類：企業根CA、企業次級CA、獨立根CA、獨立次級CA

# 安裝AD CS與架設根CA

新增 __Active Directory__  __憑證服務__ \(AD CS\)角色

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_5.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_6.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_7.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_8.png)

---

如果此電腦是獨立伺服器或您不是利用網域系統管理員身分登入的話，將無法選擇企業CA

# 憑證授權單位主控台

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_9.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_10.png)

* 企業根CA
  * 根據憑證範本來發放憑證

---

例如圖中右方的使用者範本內同時提供了可以用來將檔案加密的憑證、保護電子郵件安全的憑證與驗證用戶端身分的憑證


# 如何信任企業根CA

* AD DS會透過群組原則來讓網域內的所有使用者與電腦自動信任企業CA
  * 自動將企業CA的憑證安裝到用戶端電腦內

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_11.png)

# 如何手動信任企業或獨立CA - 1

未加入網域的電腦並未信任企業根CA

所有電腦預設也都沒有信任獨立根CA

http:// _CA_  _主機_  _\(_  _電腦_  _\)_  _名稱或_  _IP_  _位址_ /certsrv

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_12.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_13.png)

---

若用戶端為Windows Server 2019電腦的話，先將其IE增強式安全性設定關閉

* 透過 __憑證__ 管理主控台來匯入CA的憑證
  * 執行 __mmc__  __檔案__ 功能表新增/移除嵌入式管理單元從清單中選擇 __憑證__ \.\.

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_14.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_15.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_16.png)

---

使用者、電腦憑證存放區說明

# https網站憑證實例演練

* 在網站電腦上建立憑證申請檔
* 利用瀏覽器將憑證申請檔傳給CA、下載憑證檔
  * 企業CA會自動發放，故可直接下載憑證檔
  * 獨立CA預設需等管理員手動發放後，再利用瀏覽器來連接CA與下載憑證檔案
* 將SSL憑證安裝到IIS電腦，並將其繫結到網站
* 測試用戶端瀏覽器與網站之間SSL安全連線功能

* Web1先安裝好 __網頁伺服器__  __\(IIS\)__ 角色
* DNS1先安裝好DNS伺服器角色
  * 兼扮演獨立根CA，CA名稱為Sayms Standalone CA
* 在Win10PC1電腦上利用瀏覽器來連接https網站

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_17.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_18.png)

# 信任CA與建立憑證申請檔案

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_19.png)

* 讓網站與瀏覽器電腦信任CA
  * 在網站上透過「IIS管理員」建立憑證申請檔案

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_20.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_21.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_22.png)

# 申請憑證與下載憑證 - 1

先將 __IE__  __增強式安全性設定__ 關閉

http:// _CA_  _主機_  _\(_  _電腦_  _\)_  _名稱或_  _IP_  _位址_ /certsrv

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_23.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_24.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_25.png)

若是企業CA的話，畫面中還有一個 __憑證範本__ 處欄位，請在此欄位選擇 __Web__  __伺服器__

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_26.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_27.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_28.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_29.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_30.png)

# 安裝憑證

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_31.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_32.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_33.png)

---

若要在此IIS電腦內要架設多個https網站，且它們使用的IP位址與連接埠都相同的話（使用SNI功能），請選擇將它們的憑證安裝到Web Hosting憑證儲存區

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_34.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_35.png)

# 將憑證繫結到Default Web Site

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_36.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_37.png)

# 建立網站的測試網頁

* 利用 __記事本__ 新增default\.htm
  * 建議先在 __檔案總管__ 內【點選 __檢視__ 功能表勾選 __副檔名__ 】
* http://www\.sayms\.local/
* https://www\.sayms\.local/cart/

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_38.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_39.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_40.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_41.png)

# SSL連線測試

* http://www\.sayms\.local/
  * 注意網際網路暫存檔

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_42.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_43.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_44.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_45.png)

# 強制用戶端需要利用SSL連線

可以針對整個網站、單一資料夾或單一檔案來設定

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_46.png)

# CA的備份與還原

* CA資料庫與相關資料包含在 __系統__  __狀態__ 內
  * 可透過Windows Server Backup來備份系統狀態
  * 需透過 __伺服器管理員__ 來安裝Windows Server Backup功能
* 也可以透過 __憑證授權__  __單位__ 主控台

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_47.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_48.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_49.png)

# 管理憑證範本

* 企業CA根據 __憑證範本__ 來發放憑證
* 可啟用更多的憑證範本
* 變更範本或複製範本
  * 有的範本內容無法變更

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_50.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_51.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_52.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_53.png)

# 自動或手動發放憑證

使用者向企業CA申請憑證時，企業CA會驗證使用者並自動核可與發放所申請的憑證

獨立CA需手動發放，但可變更為自動發放

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_54.png)

# 撤銷憑證與CRL

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_55.png)

* 憑證有一定的有效期限，但可提前將憑證撤銷，例如員工離職、使用者憑證的金鑰洩漏等
* 被撤銷的憑證會被放入 __憑證撤銷清單__ \(CRL\)
* 解除撤銷憑證
  * 撤銷理由需為 __憑證保留__

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_56.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_57.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_58.png)

# 發佈CRL

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_59.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_60.png)

網路內的電腦需下載 __憑證撤銷清單__ \(CRL\)才可得知哪一些憑證已經被撤銷

必須先將CA的CRL 發佈出來

* 自動發佈
    * 預設每隔1週發佈一次
    * Delta CRL

# 用戶端自動下載CRL

* 瀏覽器
  * 按  \+R鍵輸入control後按Enter鍵網路和網際網路網際網路選項 __進階__ 標籤

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_61.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_62.png)

\* 憑證內會包含著CRL發佈點的資訊，因此瀏覽器可以透過

CRL發佈點來下載CRL

# 用戶端手動下載CRL

* 利用網頁瀏覽器連接CA
* 也可儲存後在檔案總管內對著該檔案按右鍵安裝CRL
* CRL檔案位於CA的% _Systemroot_ %\\System32\\Certsrv\\ CertEnroll共用資料夾內\(CertEnroll\)
  * 對著該檔案按右鍵安裝CRL

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_63.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_64.png)

# 憑證檔案的種類

| 附檔名 | .PFX | .P12 | .P7B | .CER |
| :-: | :-: | :-: | :-: | :-: |
| 憑證 |  |  |  |  |
| 私密金鑰 |  |  |  |  |
| 憑證路徑 |  |  |  |  |

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_65.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_66.png)

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_67.png)

# 匯出與匯入IIS網站的憑證

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_68.png)

# 更新憑證

CA自己的憑證與CA所發出的憑證都有一定的有效期限

根CA的憑證是自己發給自己的，而次級CA的憑證是向父CA申請的

CA所發放的憑證，其有效期限絕對不會超過CA本身的有效期限

應儘早更新CA的憑證

| 憑證種類 | 有效期限 |
| :-: | :-: |
| 根CA | 在安裝時設定，預設為5年 |
| 次級CA  | 預設最多為5年 |
| 其它的憑證 | 不一定，但大部份是1年 |

# 更新CA的憑證

可選擇是否要重新建立一組新金鑰

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_69.png)

# 更新IIS網站的憑證

![](WS2019%E7%B3%BB%E7%B5%B1%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0253-Ch16-PKI%E8%88%87HTTPS%E7%B6%B2%E7%AB%99_70.png)

