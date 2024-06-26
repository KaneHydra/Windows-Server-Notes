第 9 章 AD DS資料庫的複寫

9\-1 站台與AD DS資料庫的複寫

9\-2 預設站台的管理

9\-3 利用站台來管理AD DS複寫

9\-4 管理「通用類別目錄伺服器」

9\-5 解決AD DS複寫衝突的問題

# 站台與AD DS資料庫的複寫

* __站台__ \(site\)是由一或數個IP子網路所組成，這些子網路之間透過 __高速且可靠的連線__ \(夠快且穩定、符合您的需要\)串接起來
  * 一個LAN之內各個子網路之間的連線都符合速度快且高可靠度的要求，因此可以將一個LAN規劃為一個站台
  * WAN內各個LAN之間的連線速度一般都不快，因此WAN之中的各個LAN應分別規劃為不同的站台

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_0.png)

* AD DS大部份資料是利用 __多主機複寫模式__ 來複寫
  * 可直接更新任何一台網域控制站內的AD DS物件，之後此更新物件會被自動複寫到其他網域控制站
* 這些網域控制站是否在同一個站台，會影響到網域控制站之間AD DS資料庫的複寫行為

# 同一個站台之間的複寫

* 網域控制站之間是透過快速的連線串接在一起，因此可以有效的、快速的複寫，而且不會壓縮所傳送的資料
* 採用 __變更通知__ 方式
  * 當 __來源網域控制站__ 的AD DS資料庫內有一筆資料異動時，預設它會等15秒後，就通知位於同一個站台內的其他網域控制站
  * 收到通知的網域控制站若需此筆資料的話，就會送出 __更新資料__ 的要求給 __來源網域控制站__ ，以便開始複寫的程序

# 複寫夥伴

* 直接複寫夥伴、轉移複寫夥伴
  * 來源網域控制站只會將異動資料複寫給其 __直接複寫夥伴__
  * 每一台網域控制站內都有一個被稱為KCC的程序，它會自動建立最有效率的 __複寫拓撲__ ，也就是決定哪一些網域控制站是它的 __直接複寫夥伴__ 、而哪一些網域控制站是它的 __轉移複寫夥伴__  __。__ 以DC1來說：
    * DC2與DC7是它的 __直接__  __複寫夥伴__
    * DC3、DC4、DC5、DC6是它的 __轉移__  __複寫夥伴__

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_1.png)

# 如何減少複寫延遲時間 - 1

* 從 __來源網域控制站__ 內的AD DS資料有異動開始，到這些資料被複寫到所有其他網域控制站之間的間隔時間不要太久
* KCC在建立 __複寫拓撲__ 時
  * __來源網域控制站__ → __目的網域控制站__ ：跳躍的網域控制站數量不超過3台
  * 例如從DC1到DC4跳躍了3台網域控制站\(DC2、DC3、DC4\)、從DC1到DC5也只跳躍了3台網域控制站\(DC7、DC6、DC5\)
* 避免 __來源網域控制站__ 負擔過重
  * __來源網域控制站__ 並不是同時通知其所有的 __直接複寫夥伴__ ，而是會間隔3秒，也就是先通知第1 台 __直接複寫夥伴__ ，間隔3秒後再通知第2台…

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_2.png)

當網域控制站的數量有異動時，KCC會重新建立 __複寫拓撲__ ，而且仍然會遵照 __跳躍的網域控制站數量不超過__  __3__  __台__ 的原則

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_3.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_4.png)

__緊急複寫__ ：對某些重要的更新資料來說，系統並不會等15秒鐘才通知其 __直接複寫夥伴__ ，而是立刻通知。這些重要的更新資料包含使用者帳戶被鎖定、帳戶鎖定原則有異動、網域的密碼原則有異動等

# 不同站台之間的複寫

* 不同站台之間的連線速度不夠快
  * 為了降低對連線頻寬的影響，故站台之間的AD DS資料在複寫時會被壓縮，而且資料的複寫是採用 __排定時程__ 的方式
  * 應儘量排定在站台之間連線的離峰時期才執行複寫工作，同時複寫頻率也不要太高
* 每一個站台內都各有一台被稱為 __站台間拓撲產生器__ 的網域控制站，它負責建立 __站台之間的複寫拓撲__ 、從其站台內挑選一台網域控制站來扮演 __bridgehead__  __伺服器__ 角色

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_5.png)

# 複寫通訊協定

* RPC over IP \(Remote Procedure Call over Internet Protocol\)
  * 無論是同一個站台之間或不同站台之間，都可以利用RPC over IP來執行AD DS資料庫的複寫動作
  * 為確保資料在傳送時的安全性，它會驗證身分與將資料加密
* SMTP \(Simple Mail Transfer Protocol\)
  * 只能用來執行不同站台之間的複寫
  * 若不同站台的網域控制站之間無法直接溝通，或之間的連線品質不穩定時，就可以透過SMTP來傳送：
    * 只能夠複寫 __架構目錄分割區__ 、 __設定目錄分割區__ 與 __應用程式目錄分割區__ ，不能夠複寫 __網域目錄分割區__
    * 需向 __企業__  __CA__ \(Enterprise CA\)申請憑證，因為在複寫過程中，需要利用憑證來驗證身分

# 預設的站台

* 在建立第1個網域\(樹系\)時，系統就會自動建立預設站台
  * Default\-First\-Site\-Name
  * Servers
  * Inter\-Site Transports
  * Subnets
* 在安裝網域控制站時，此網域控制站的電腦帳戶就會自動被放到此子網路所隸屬的站台內\(從IP位址的網路識別碼來判斷\)
  * 系統預設並沒有在AD DS內建立任何的子網路，因此網域控制的電腦帳戶會被放到Default\-First\-Site\-Name站台內，例如圖中的DC1 – DC6

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_6.png)

# Servers資料夾與複寫設定

* 以圖中的DC2為例，其包含2個由KCC所自動建立的 __連線物件__ ，分別來自DC1與DC3
  * DC2會直接接收由這2台網域控制站所複寫過來的AD DS資料
  * 這2台網域控制站都是DC2的 __直接複寫夥伴__
  * 點取其他任何一台網域控制站時，也可以看到它們與 __直接複寫夥伴__ 之間的 __連線物件__

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_7.png)

# 手動建立連線物件

網域控制站相互之間的 __連線物件__ ，都會由KCC負責自動建立與維護，而且是雙向的

可視需要來手動建立 __連線物件__

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_8.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_9.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_10.png)

# 複寫時機

AD DS資料有異動時，預設15 秒鐘後通知同一站台的 __直接複寫夥伴__

即使沒有異動資料，預設也會每隔一小時自動執行一次複寫工作

也可以手動立刻複寫

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_11.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_12.png)

# 利用站台來管理AD DS複寫

* 站台之間除了實體連線\(WAN link\)外，還需建立邏輯的 __站台連結__ \(site link\)才可以進行AD DS資料庫的複寫
  * 預設已經建立一個名稱為DEFAULTIPSITELINK的站台連結\(使用IP複寫通訊協定\)
* 建立SiteA與SiteB時，必須透過 __站台連結__ 將這兩個站台邏輯的連接在一起，它們之間才可以進行AD DS資料庫的複寫

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_13.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_14.png)

# 建立新站台

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_15.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_16.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_17.png)

# 建立IP子網路

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_18.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_19.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_20.png)

# 建立站台連結

* SiteA與SiteB已經透過DEFAULTIPSITELINK邏輯的連接在一起
  * 可透過以下練習來將其改為透過SiteLinkAB來連接

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_21.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_22.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_23.png)

---

由於我們在前面建立SiteA與SiteB時，都已經將SiteA與SiteB歸納到DEFAULTIPSITELINK這個站台連結，也就是說這兩個站台已經透過DEFAULTIPSITELINK邏輯的連接在一起了。我們透過以下練習來將其改為透過SiteLinkAB來連接

# 將網域控制站搬移到所屬的站台

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_24.png)

* 網域控制站DC1、DC2與DC3的網路識別碼都是192\.168\.8\.0
  * 故需將DC1、DC2與DC3搬移到站台SiteA
  * 以後若在 __台北__ 網路內安裝新網域控制站的話，則該網域控制站的電腦帳戶會自動被放置到SiteA內

* DC4、DC5與DC6的網路識別碼都是192\.168\.9\.0
  * 故需將DC4、DC5與DC6搬移到站台SiteB
  * 以後若在 __高雄__ 網路內所安裝的新網域控制站，其電腦帳戶會自動被放到SiteB內

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_25.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_26.png)

---

您可以在SiteA與SIteB之間架設一台由Windows Server所扮演的路由器，來模擬演練SiteA與SIteB是位於兩個不同網路的環境

# 指定「喜好的bridgehead伺服器」

* 可以自行選擇扮演 __bridgehead__  __伺服器__ 的網域控制站
* 要查看 __喜好的__  __bridgehead__  __伺服器__ 清單
  * 展開Inter\-Site Transports對著IP按右鍵內容點選 __屬性編輯器__ 標籤按篩選鈕點選 __顯示唯讀屬性__ 處的 __反向連結__ 雙擊屬性清單中的bridgeheadServerListBL

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_27.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_28.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_29.png)

---

非必要請不要自行指定喜好的bridgehead伺服器，因它會讓KCC停止自動挑選bridgehead伺服器，此時若您所選擇的喜好的bridgehead伺服器都故障時，KCC不會再自動挑選bridgehead伺服器，
若您要將扮演喜好的bridgehead伺服器的網域控制站搬移到其他站台的話，請先取消其喜好的bridgehead伺服器的角色後再搬移

# 站台連結與AD DS資料庫的複寫設定

* 變更站台連結中的站台成員
* 花費\(cost\)
  * 考慮實體WAN link 的連線頻寬、穩定性、延遲時間與費用
  * KCC在建立 __複寫拓撲__ 時，會選擇 __花費__ 較低的網域控制站來當作 __直接複寫夥伴__
  * 使用者在登入時，若需找尋其他站台的網域控制站的話，會選擇 __花費__ 較低的網域控制站
* 複寫間隔為每…分鐘、變更排程

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_30.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_31.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_32.png)

# 站台連結橋接器

* __站台連結__ 具備 __轉移性__
  * 系統預設會自動橋接所有的 __站台連結__
  * 圖中SiteLinkAB的花費為3、SiteLinkBC的花費為4，則SiteLinkBridgeABC的花費是3 \+ 4 =7
  * 此花費高於SiteLinkAB\(3\)與SiteLinkBC\(4\)，故在建立 __複寫拓撲__ ，預設不會在DC1與DC3之間建立 __連線物件__ \(不會將DC1與DC3設為 __直接複寫夥伴__ \)，除非DC2無法使用\(例如電腦故障、離線\)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_33.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_34.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_35.png)

# 手動建立站台連結橋接器

* 想要控制AD DS資料複寫方向或兩個站台之間受到限制無法直接溝通
  * 例如SiteB的防火牆限制SiteA的電腦不可與SiteC的電腦溝通，則圖中的SiteLinkBridgeABC就沒有意義了，因為SiteA將無法直接與SiteC進行AD DS資料庫複寫
  * 此時若SiteA還可以透過另外一個站台SiteD來與SiteC溝通的話，我們就沒有必要讓KCC浪費時間建立SiteLinkBridgeABC，或浪費時間嘗試透過SiteLinkBridgeABC來複寫AD DS資料庫
* 自行建立SiteLinkBridgeADC
  * 假設SiteA還可以透過另外一個站台SiteD來與SiteC溝通的話

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_36.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_37.png)

# 站台連接橋接器範例一 - SiteA與SiteC之間有站台連接

* 若DC2正常運作，且DC1、DC2與DC3隸屬於同一個網域
  * 因SiteLinkBridgeABC花費較高\(7\)，因此不會將DC1與DC3設為 __直接複寫夥伴__ ，所以它們在複寫AD DS資料庫時需透過DC2來轉送
  * DC1→DC2：「1:00 PM – 4:00 PM」、DC2→DC3：「2:00 PM – 6:00 PM」
* 若DC2離線，或DC2與DC1/DC3不是隸屬於同一個網域
  * 因此DC1與DC3之間需直接複寫，此時因SiteA與SiteC之間有 __站台橋接連接器__ ，所以KCC會將DC1與DC3設定為 __直接複寫夥伴__ ，讓它們之間可以直接複寫
  * 複寫時段：「 2:00 PM – 4:00 PM 」、複寫間隔時間： 60分鐘\(取兩者間最大值\)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_38.png)

\* 此範例假設是針對

__  __  __網域目錄分割區__ 來說明

* 若DC2正常運作，且DC1、DC2與DC3隸屬於同一個網域
  * 因SiteA與SiteC沒有站台連接，因此建立 __複寫拓撲__ 時，並不會將DC1與DC3設為 __直接複寫夥伴__ ，所以它們在複寫AD DS資料庫時只能夠透過DC2來轉送
* 若DC2離線，或DC2與DC1/DC3不是隸屬於同一個網域
  * 此時DC2無法接收、儲存DC1/DC3的AD DS資料，因此DC1與DC3必須直接複寫AD DS資料，但此時因SiteA與SiteC之間沒有 __站台連接__ ，故無法將DC1與DC3設定為 __直接複寫夥伴__ ，所以DC1與DC3之間將無法複寫AD DS資料

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_39.png)

---

SiteA與SiteB之間、SiteB與SiteC之間分別建立了站台連接，但是卻取消勾橋接所有站台連接，且沒有自行建立站台橋接連接器

# 通用類別目錄伺服器

* 一般網域控制站內只會儲存所屬網域內 __網域目錄分割區__ 的完整資料
* __通用類別目錄伺服器__ 還會儲存樹系中所有其他網域之 __網域目錄分割區__ 的物件的部份屬性
  * 讓使用者可以透過這些屬性，很快速的找到位於其他網域內的物件
  * 系統預設會將使用者常用來搜尋的屬性加入到 __通用類別目錄__ 內，例如登入帳戶名稱、UPN、電話號碼等

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_40.png)

# 新增屬性到通用類別目錄內

* 可以自行利用 __Active Directory__  __架構__ 主控台來將其他屬性加入到 __通用類別目錄__ 內
  * 需要在網域控制站上先執行 __regsvr32 schmmgmt\.dll__ 指令來登錄 __schmmgmt\.dll__
  * 再透過新增 __Active Directory__  __架構__ 嵌入式管理單元來新增

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_41.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_42.png)

# 通用類別目錄的功能

快速尋找物件

提供UPN的驗證功能

提供萬用群組的成員資料

# 快速尋找物件

__通用類別目錄__ 內儲存著樹系中所有網域之 __網域目錄分割區__ 的物件的部份屬性，讓使用者可以利用這些屬性很快速找到位於其他網域的物件

__通用類別目錄__ 所使用的TCP連接埠號碼為3268

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_43.png)

# 提供UPN的驗證功能

* 當使用者利用UPN登入時，若負責驗證使用者身分的網域控制站無法從其AD DS資料庫來得知該使用者是隸屬於哪一個網域的話，它可以向 __通用類別目錄伺服器__ 詢問
  * 例如使用者利用其UPN george@sayms\.local帳戶到網域hk\.sayiis\.local的成員電腦上登入時
* 若使用者的UPN為george@sayms\.local，則該使用者帳戶就一定是在網域sayms\.local的AD DS資料庫嗎?
  * 不一定\! 使用者帳戶的UPN尾碼預設是帳戶所在網域的網域名稱，但尾碼可以變更，且使用者帳戶被搬移到其他網域時，其UPN並不會自動改變

# 提供萬用群組的成員資料

* 使用者登入時，系統會為其建立access token\(內含使用者所隸屬群組的SID\)
  * 系統需得知使用者隸屬於哪一些群組，而負責驗證登入身分的網域控制站需向 __通用類別目錄伺服器__ 查詢他隸屬於哪一些萬用群組，以便建立access token、讓使用者完成登入程序
* 使用者登入時，若找不到 __通用類別目錄伺服器__ 的話，是否可以登入成功呢?
  * 若使用者之前曾經在這台電腦成功登入過的話，還是可以登入成功
  * 若使用者之前未曾在這台電腦登入過的話，則使用者無法登入
  * \*若使用者是隸屬於Domain Admins群組的成員，則無論 __通用類別目錄__ 是否在線上，他都可以登入
* 若要將某台網域控制站設定為 __通用類別目錄伺服器__ 的話

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_44.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_45.png)

# 萬用群組成員快取

對一個小型站台來說，由於硬體配備有限、經費短缺、頻寬不足等因素的影響，因此您可能不想在此站台架設一台 __通用類別目錄伺服器__

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_46.png)

* __萬用群組成員快取__ 的好處
  * 加快使用者登入的速度
  * 現有網域控制站的硬體不需要升級
  * 減輕對網路頻寬的負擔

# 啟用萬用群組成員快取

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_47.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_48.png)

* 預設是每隔8小時向 __通用類別目錄伺服器__ 索取一次最新資料\(更新快取\)
  * 它是從哪一個站台的 __通用類別目錄伺服器__ 來更新快取資料呢？這可從圖中最下方的 __在站台通用類別目錄中重新整理快取__ \(Refresh Cache from\)來選擇

# 解決AD DS複寫衝突的問題

* AD DS資料庫內的大部份資料是利用 __多主機複寫模式__ 來複寫
  * 若兩位系統管理員同時分別在兩台網域控制站建立相同的物件，或是修改相同物件的話，則之後雙方開始相互複寫這些物件時，就會有衝突發生
* 屬性戳記
  * 在解決衝突時，是以戳記值最高的優先
    * 版本號碼較高的優先→修改時間較後的優先→網域控制站的GUID數值較高者優先

| 版本號碼 | 修改時間 | 網域控制站的GUID |
| :-: | :-: | :-: |


* 衝突的種類
  * 屬性值相衝突
  * 在某容區內新增物件或將物件搬移到此容區內，但是這個容區已經在另外一台網域控制站內被刪除
  * 名稱相同

# 屬性衝突的解決方法

若屬性值有衝突，則以戳記值最高的優先

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_49.png)

* 查看版本號碼
  * repadmin  /showmeta  CN=王喬治\,OU=業務部\,DC=sayms\,DC=local

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_50.png)

---

Repadmin.exe還可以用來查看網域控制站的複寫拓撲、建立連線物件、手動執行複寫、查看複寫資訊、查看網域控制站的GUID等

# 物件存放容區被刪除的解決方法

* 例如：
  * 第1台網域控制站：甲系統管理員上將組織單位 __會計部__ 刪除
  * 第2台網域控制站：乙系統管理員在組織單位 __會計部__ 內新增一個使用者帳戶 __高麗黛__
* 此時所有網域控制站內的組織單位 __會計部__ 都會被刪除，但是使用者帳戶 __高麗黛__ 會被放置到LostAndFound資料夾

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_51.png)

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_52.png)

---

若要練習驗證上述理論的話，請先讓兩台網域控制站之間網路無法溝通、然後分別在兩台網域控制站上操作、再讓兩台網域控制站能夠透過網路正常溝通、手動複寫AD DS資料庫。
不過請先取消勾選保護以防止被意外刪除，否則無法刪除組織單位會計部

# 名稱相同的解決方法

* 兩個物件都會被保留
  * 戳記值較高的物件名稱會維持原來的名稱
  * 戳記值較低的物件名稱會被改變
    * _物件的_  _RDN  _  __CNF__  __：__  _物件的_  _GUID_

![](WS2022AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA0273-Ch09-AD%20DS%E8%B3%87%E6%96%99%E5%BA%AB%E7%9A%84%E8%A4%87%E5%AF%AB_53.png)

