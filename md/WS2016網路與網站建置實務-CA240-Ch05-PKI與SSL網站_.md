第 5 章 PKI與SSL網站

5\-1 PKI概觀

5\-2 憑證授權單位\(CA\)概觀與根CA的安裝

5\-3 SSL網站憑證實例演練

5\-4 次級CA的安裝

5\-5 憑證的管理

# PKI概觀

* PKI的功能
  * 將傳送的資料加密\(encryption\)
  * 驗證資料是否由寄件者所傳來\(authentication\)
  * 確認資料的完整性\(integrity\)
* 需要一組金鑰
  * 公開金鑰\(public key\)：可以公開給其他的使用者
  * 私密金鑰\(private key\)：使用者私有、儲存在使用者電腦內，只有該使用者能夠存取
* 需要向憑證授權單位\(CA\)申請憑證\(certificate\)來擁有與使用這一組金鑰

# 公開金鑰加密法

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_0.png)

* 資料被加密後，必須經過解密才讀得到資料的內容
* 公開金鑰加密法
  * 使用一組金鑰:公開金鑰與私密金鑰
  * 公開金鑰用來加密、私密金鑰用來解密
  * 又稱為 __非對稱式__ \(asymmetric\)加密法
* 秘密金鑰加密法
  * 又稱為 __對稱式__ \(symmetric\)加密法
  * 加密、解密都是使用同一個金鑰

# 公開金鑰驗證法

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_1.png)

* 收件者在收到經過 __數位__  __簽章__ 的資料時
  * 可透過數位簽章來驗證此資料是否確實是由寄件者本人所寄出
  * 可檢查資料在傳送的過程中是否被動過手腳

# 數位簽章的運作程序

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_2.png)

# SSL網站安全連線

* 需替網站向 __憑證授權單位__ 申請SSL憑證
  * 憑證內包含了公開金鑰、憑證有效期限、發放此憑證的CA、CA的數位簽章等
* 網站擁有SSL憑證之後
  * 用戶端與網站間可以透過SSL安全連線來溝通
  * https://www\.sayms\.local/

# 如何建立SSL安全連線

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_3.png)

# 伺服器名稱指示(SNI)

若不支援SNI：網站伺服器無法從此訊息來判斷用戶端是要連接網站一或網站二

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_4.png)

* SNI：用戶端所送出「Client Hello」的訊息內包含網站的主機名稱，而且網站伺服器也能夠透過此名稱來得知用戶端所欲連接的網站
  * IIS 8\(含\)以後的版本支援SNI\(Windows Server 2016 IIS為IIS 10\)；大部分用戶端瀏覽器支援SNI

可利用IP位址來區別網站，但是無法透過第4章所述的主機名稱來區別網站，因為此主機名稱是在http封包內，然而用戶端是在完成圖中6個步驟的協商之後，才會送出經過SSL加密的http封包（https），在此之前網站伺服器並無法得知主機名稱

---

Windows XP內的Internet Explorer不支援SNI，不論Internet Explorer的版本為何

# 「憑證授權單位(CA)」概觀

* 負責發放憑證的機構
* 電子郵件保護或SSL網站安全連線，都必須擁有憑證
* 公開金鑰與私密金鑰的產生
  * 申請憑證時會將姓名、地址與電子郵件信箱等資料傳送到稱為CSP的程式，此程式已經安裝在申請者的電腦內或此電腦可以存取的設備內
  * CSP會自動建立公開金鑰與私密金鑰、將私密金鑰儲存到申請者電腦的登錄中、將憑證申請資料與公開金鑰一併傳送到CA、CA利用自己的私密金鑰將要發放的憑證加以簽章，然後發放此憑證
  * 申請者收到憑證後，將憑證安裝到其電腦內
* 憑證內包含了憑證的發放對象\(使用者或電腦\)、申請者的姓名、地址、電子郵件信箱、公開金鑰、憑證有效期限、發放此憑證的CA與CA的數位簽章等資料

---

若有安裝讀卡機的話，可以利用智慧卡來登入，不過也需要透過類似程序來申請憑證，CSP會將私密金鑰儲存到智慧卡內

# CA的信任

* 電子郵件
  * 收件者的電腦應該要信任由CA所發放的憑證
* 連接SSL網站
  * 用戶端電腦應該信任發放SSL憑證給此網站的CA
* Windows系統預設已自動信任知名CA

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_5.png)

# 申請憑證

* 可向知名CA申請憑證
  * 適合於對外界溝通來使用
* 自行架設CA
  * 適合於企業內各分公司之間、與事業合作夥伴之間、與供應商之間、與客戶之間來使用
  * 安裝Windows Server 2016的Active Directory憑證服務\(AD CS\)
  * 需設定讓電腦來信任此CA
  * CA的種類：企業根CA、企業次級CA、獨立根CA、獨立次級CA

# 企業CA

* 需AD DS
* 可將其安裝到網域控制站或成員伺服器
* 發放憑證的對象是網域使用者
  * 使用者需提供網域使用者帳戶與密碼
  * 企業CA可以從AD DS來得知該使用者的相關資訊，並據以決定該使用者是否有權利來申請所需憑證
* 企業根與企業次級CA
  * 企業根CA主要是用來發放憑證給次級CA
  * 企業次級CA適合於用來發放保護電子郵件安全、網站SSL安全連線等憑證
  * 企業次級CA必須向其父系CA\(例如企業根CA\)取得憑證
  * 企業次級CA也可以發放憑證給其下一層的次級CA

# 獨立CA

* 可將其安裝到獨立伺服器、成員伺服器或網域控制站
* 無論是否網域使用者，都可以向獨立CA申請憑證
* 獨立根與獨立次級CA
  * 獨立根CA主要是用來發放憑證給次級CA
  * 獨立次級CA適合於用來發放保護電子郵件安全、網站SSL安全連線等憑證
  * 獨立次級CA必須向其父系CA\(例如企業根CA\)取得憑證
  * 獨立次級CA也可以發放憑證給其下一層的次級CA

# 安裝AD CS與架設根CA

新增 __Active Directory__  __憑證服務__ \(AD CS\)角色

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_6.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_7.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_8.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_9.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_10.png)

---

如果此電腦是獨立伺服器或您不是利用網域系統管理員身分登入的話，將無法選擇企業CA

# 憑證授權單位主控台

* 企業根CA
  * 根據 __憑證範本__ 來發放憑證

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_11.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_12.png)

---

例如圖中右方的使用者範本內同時提供了可以用來將檔案加密的憑證、保護電子郵件安全的憑證與驗證用戶端身分的憑證


# 如何信任企業根CA

* AD DS會透過群組原則來讓網域內的所有使用者與電腦自動信任企業CA
  * 自動將企業CA的憑證安裝到用戶端電腦內

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_13.png)

# 如何手動信任企業或獨立CA - 1

* 未加入網域的電腦並未信任企業根CA
* 所有電腦預設也都沒有信任獨立根CA
  * 可另外透過群組原則來讓網域內所有電腦自動信任獨立CA
* http:// _CA_  _主機_  _\(_  _電腦_  _\)_  _名稱或_  _IP_  _位址_ /certsrv

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_14.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_15.png)

---

若用戶端為Windows Server 2016電腦的話，先將其IE增強式安全性設定關閉

* 透過 __憑證__ 管理主控台來匯入CA的憑證
  * 執行 __mmc__  __檔案__ 功能表新增/移除嵌入式管理單元從清單中選擇 __憑證__ \.\.

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_16.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_17.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_18.png)

---

使用者、電腦憑證存放區說明

# SSL網站憑證實例演練

* 在網站電腦上建立憑證申請檔
* 利用瀏覽器將憑證申請檔傳給CA、下載憑證檔
  * 企業CA會自動發放憑證，故可直接下載憑證檔
  * 獨立CA預設需等管理員手動發放憑證後，再利用瀏覽器來連接CA與下載憑證檔案
* 將SSL憑證安裝到IIS電腦，並將其繫結到網站
* 測試用戶端瀏覽器與網站之間SSL安全連線功能

* Web1先安裝好 __網頁伺服器__  __\(IIS\)__ 角色
* DNS1先安裝好DNS伺服器角色
  * 建立正向對應區域sayms\.local
  * 建立主機記錄www、其IP位址為192\.168\.8\.1
  * 兼扮演獨立根CA，CA名稱為Sayms Standalone CA
* 在Win8PC1電腦上利用瀏覽器來連接SSL網站

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_19.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_20.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_21.png)

# 信任CA與建立憑證申請檔案

* 讓網站與瀏覽器電腦信任CA
  * 在網站上透過「IIS管理員」建立憑證申請檔案

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_22.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_23.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_24.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_25.jpg)

# 申請憑證與下載憑證 - 1

先將 __IE__  __增強式安全性設定__ 關閉

http://CA主機\(電腦\)名稱或IP位址/certsrv

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_26.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_27.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_28.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_29.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_30.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_31.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_32.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_33.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_34.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_35.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_36.png)

# 安裝憑證

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_37.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_38.png)

---

若要在此IIS電腦內要架設多個SSL網站，且它們使用的IP位址與連接埠都相同的話（使用SNI功能），請選擇將它們的憑證安裝到Web Hosting憑證儲存區

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_39.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_40.png)

# 將憑證繫結到Default Web Site

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_41.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_42.png)

# 建立網站的測試網頁

* 利用 __記事本__ 新增default\.htm
  * 建議先在 __檔案總管__ 內【點選 __檢視__ 功能表勾選 __副檔名__ 】
* http://www\.sayms\.local/
* https://www\.sayms\.local/cart/

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_43.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_44.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_45.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_46.png)

# SSL連線測試

* http://www\.sayms\.local/
  * 注意網際網路暫存檔

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_47.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_48.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_49.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_50.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_51.png)

# 強制用戶端需要利用SSL連線

可以針對整個網站、單一資料夾或單一檔案來設定

# 架設企業次級CA

企業內部應該將保護電子郵件安全、網站SSL安全連線等憑證的發放工作交給次級CA來執行

安裝步驟大致上與企業根CA相同，但企業次級CA必須向其父系CA取得憑證

AD DS網域內的成員電腦會自動信任企業次級CA

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_52.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_53.png)

# 獨立次級CA的憑證與CRL

獨立次級CA必須向其父系CA取得憑證

CA服務啟動時必須從其父系CA取得 __憑證撤銷清單 __ \(CRL\)，否則無法啟動

# CRL發佈點

* 父系CA透過 __CRL__  __發佈點__ \(CDP\)來告知次級CA或應用程式從何處下載CRL
* 父系CA可透過以下路徑來指定 __CRL__  __發佈__  __點__
  * 透過LDAP路徑
    * 適合於網域成員電腦之間
  * 透過FILE路徑
    * 共用資料夾
    * 適合於非網域成員之間或兩者之中有一個不是網域成員
  * 透過HTTP路徑
    * 父系CA需架設IIS網站
    * 適合於非網域成員之間或兩者之中有一個不是網域成員

# 指定CRL發佈點 - 1

* 假設獨立次級CA的父系CA是企業根CA，且採用HTTP路徑來從企業根CA下載CRL
* 到企業根CA選擇利用HTTP路徑來指定 __CRL__  __發佈__  __點__
  * 開始憑證授權單位對著CA按右鍵內容
  * 完成後，次級CA便可透過圖中HTTP網址來下載CRL與AIA

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_54.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_55.png)

---

授權資訊存取 (AIA)內含何處可以找到CA最新憑證的資訊

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_56.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_57.png)

# 指定CRL發佈點 - 2

* 執行PKIVIEW\.MSC來確認3個http路徑都存在
  * PKIVIEW\.MSC僅適用於企業CA
* 若CDP位置或DeltaCRL位置有短少的話

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_58.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_59.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_60.png)

# 架設獨立次級CA

需 __安裝__  __Active __  __Directory__  __憑證__  __服務__  __\(__ AD CS\)角色，安裝步驟大致上與獨立根CA相同，但需向其父系CA取得憑證

先將IE ESC停用

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_61.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_62.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_63.png)

# 向父系CA申請、下載與安裝憑證 - 1

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_64.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_65.png)

若無法從父系CA的 __CRL__  __發佈點__ 下載 __憑證撤銷__  __清單__ 的話

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_66.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_67.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_68.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_69.png)

---

可能需要先利用ipconfig  /flushdns指令清除DNS快取區

# CA的備份與還原

* CA資料庫與相關資料包含在 __系統__  __狀態__ 內
  * 可透過Windows Server Backup來備份系統狀態
  * 需透過 __伺服器管理員__ 來安裝Windows Server Backup功能
* 也可以透過 __憑證授權__  __單位__ 主控台

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_70.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_71.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_72.png)

# 管理憑證範本

* 企業CA根據 __憑證範本__ 來發放憑證
* 可啟用更多的憑證範本
* 變更範本或複製範本
  * 有的範本內容無法變更

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_73.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_74.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_75.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_76.png)

# 自動或手動發放憑證

使用者向企業CA申請憑證時，企業CA會驗證使用者並自動核可與發放所申請的憑證

獨立CA需手動發放，但可變更為自動發放

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_77.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_78.png)

# 撤銷憑證與CRL

* 憑證有一定的有效期限，但可提前將憑證撤銷，例如員工離職、使用者憑證的金鑰洩漏等
* 被撤銷的憑證會被放入 __憑證撤銷清單__ \(CRL\)
* 解除撤銷憑證
  * 撤銷理由需為 __憑證保留__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_79.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_80.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_81.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_82.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_83.png)

# 發佈CRL

網路中的電腦需下載 __憑證撤銷清單__ \(CRL\)才可得知哪一些憑證已經被撤銷

必須先將CA的CRL 發佈出來

* 自動發佈
    * 預設每隔1週發佈一次
    * Delta CRL

# 用戶端自動下載CRL

* 瀏覽器
  * 按  \+R鍵輸入control後按Enter鍵網路和網際網路網際網路選項 __進階__ 標籤

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_84.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_85.png)

\* 憑證內會包含著CRL發佈點的資訊，因此瀏覽器可以透過

CRL發佈點來下載CRL

# 用戶端手動下載CRL

* 利用網頁瀏覽器連接CA
* 也可儲存後在檔案總管內對著該檔案按右鍵安裝CRL
* CRL檔案位於CA的% _Systemroot_ %\\System32\\Certsrv\\ CertEnroll共用資料夾內\(CertEnroll\)
  * 對著該檔案按右鍵安裝CRL

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_86.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_87.png)

# 憑證檔案的種類

| 附檔名 | .PFX | .P12 | .P7B | .CER |
| :-: | :-: | :-: | :-: | :-: |
| 憑證 |  |  |  |  |
| 私密金鑰 |  |  |  |  |
| 憑證路徑 |  |  |  |  |

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_88.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_89.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_90.png)

# 匯出與匯入IIS網站的憑證

利用 __IIS__  __管理員__

利用 __憑證__ 嵌入式管理單元

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_91.png)

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

* 可選擇是否要重新建立一組新金鑰
* 企業次級CA
  * 線上更新或離線更新
* 獨立次級CA
  * 離線更新

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_92.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_93.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_94.png)

# 更新IIS網站的憑證

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch05-PKI%E8%88%87SSL%E7%B6%B2%E7%AB%99_95.png)

