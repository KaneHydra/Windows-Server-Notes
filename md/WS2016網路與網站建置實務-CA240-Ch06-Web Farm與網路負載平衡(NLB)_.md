第 6 章 Web Farm與網路負載平衡\(NLB\)

6\-1 Web Farm與網路負載平衡概觀

6\-2 Windows系統的網路負載平衡概觀

6\-3 IIS網頁伺服器的Web Farm實例演練

6\-4 Windows NLB叢集的進階管理

# Web Farm概觀

將多台IIS網頁伺服器組成Web Farm，它們將同時對使用者提供一個不中斷的、可靠的網站服務

提高網頁存取效率

具備容錯功能

# Web Farm的架構

防火牆

負載平衡器

前端Web Farm\(IIS網頁伺服器\)

後端資料庫伺服器

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_0.png)

# 使用Windows NLB的Web Farm

使用Windows Server 2016內建的網路負載平衡功能\(Network Load Balancing、Windows NLB或WNLB\)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_1.png)

# 透過防火牆來支援Web Farm

* 利用防火牆的發行規則來支援Web Farm
  * 防火牆將外部連接內部網站的要求轉交給Web Farm中的一台網頁伺服器來處理
  * 防火牆具備自動偵測網頁伺服器是否停止服務的功能

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_2.png)

# 將網頁內容儲存到本機磁碟

* 需讓每一台網頁伺服器內所儲存的網頁內容都相同
  * 可利用手動複製的方式
  * 可採用DFS\(分散式檔案系統\)的自動複製方式

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_3.png)

# 將網頁內容儲存到SAN或NAS

將網頁儲存到SAN\(Storage Area Network\)或NAS\(Network Attached Storage\)儲存裝置內，並利用它們來提供網頁內容的容錯功能

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_4.png)

# 網頁儲存到檔案伺服器

應架設多台檔案伺服器來提供容錯功能

所有伺服器的網頁內容需相同\(可利用 __DFS__  __複寫__ \)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_5.png)

# 利用Windows NLB建置Web Farm

* 固定IP位址
  * 每一台網頁伺服器的外網卡各有一個固定IP位址
  * 對外流量透過固定IP位址送出
* 叢集IP位址
  * 所有伺服器另外共用一個相同的 __叢集__  __IP__  __位址__ \( __虛擬__  __IP__  __位址__ \)
  * 透過叢集IP位址來接收外部來的上網要求

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_6.png)

# Windows NLB的容錯功能

* 心跳\(heartbeat\)
  * NLB叢集伺服器會隨時監聽其他伺服器的 __心跳__ ，以便確認對方是否正常
* 交集 \(convergence\)
  * 叢集成員有異動時\(伺服器故障、新增伺服器等\)，NLB會啟動 __交集__ 程序
  * 讓叢集內所有伺服器擁有一致的狀態與重新分配工作負擔
  * __交集__ 時，正常伺服器仍會繼續服務、正在處理中的要求也不會受到影響
  * __交集__ 完成後，連接Web Farm要求，會重新分配給仍正常的伺服器來負責

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_7.png)

# Windows NLB的親和性

* 定義來源主機與NLB叢集成員之間的關係
* 分為3種親和性
  * 無\(None\)
    * 根據來源主機的IP位址與連接埠
    * 伺服器的 __主機識別元__ \(host ID\)/雜湊值\(hash\)
    * 同一台主機所提出的多個連接Web Farm要求，可能會分別由不同的網頁伺服器負責
  * 單一\(Single\)
    * 根據來源主機的IP位址
    * 同一台主機所提出的所有連接Web Farm要求，都會由同一台伺服器負責
  * 網路\(Network\)
    * 根據來源主機的IP位址中最高3個位元組
    * 例如IP位址為201\.11\.22\.1 － 201\.11\.22\.254
* 可透過 __連接埠規則__ 來改變親和性
  * 例如將特定流量指定由優先順序較高的單一台伺服器來負責處理\(此時該流量將不再具備負載平衡功能\)

# Windows NLB的操作模式

單點傳播模式\(unicast mode\)

多點傳送模式\(multicast mode\)

# 單點傳播模式(unicast mode)

* 叢集內每一台網頁伺服器的網路卡的MAC位址會被替換成一個相同的 __叢集__  __MAC__  __位址__
* 透過 __叢集__  __MAC__  __位址__ 來接收連接Web Farm要求
  * 此要求會被送到叢集中的每一台網頁伺服器
* 有一些狀況必須解決

# 交換器每一個port的MAC位址必須唯一

兩台伺服器的MAC位址都被改為叢集MAC位址，而交換器的每一個port所登記的MAC位址必須是唯一的

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_8.png)

# 利用MaskSourceMAC解決問題

將叢集MAC位址中最高第2組字元改為 __主機識別元__ ，然後將此新MAC位址當作外送封包的來源MAC位址

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_9.png)

# Switch Flooding的問題

* 叢集IP位址220\.10\.11\.5 \-> 叢集MAC位址
  * 封包會被送到switch的所有port
* 屬正常現象，因為它讓送到此叢集的封包，能夠被送到叢集中的每一台伺服器
* 但會對連接在switch的非叢集成員造成額外的網路負擔，甚至會因為這些電腦也收到專屬於叢集的機密封包，而有安全上的顧慮

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_10.png)

＊ 有一種網路攻擊行為會將交換器內儲存MAC與port對照表的有限記憶體空間填滿，使得交換器無法記錄新的對照資料而進入Switch Flooding的狀態

# Switch Flooding的解決方法

* 將NLB叢集內所有伺服器連接到集線器\(hub\)，然後再將集線器連接到交換器中的一個port
* 停用MaskSourceMAC
  * __HKEY\_LOCAL\_MACHINE\\SYSTEM\\__  __CurrentControlSet__  __\\Services\\WLBS\\Parameters\\Interface\\__  _Adapter\-GUID\\_  __MaskSourceMAC__
* 也可以透過VLAN\(虛擬區域網路\)技術來解決問題

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_11.png)

# 叢集伺服器之間無法相互溝通

除了與NLB有關的流量之外\(heartbeat\)，叢集伺服器之間無法相互溝通

若將將網頁內容直接放在網頁伺服器的話，伺服器之間將無法透過 __DFS__  __複寫__ 功能來自動讓網頁內容一致

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_12.png)

# 伺服器之間相互溝通的解決方法

* 每一台伺服器各另外安裝一片網路卡
  * 這片網路卡不要啟用NLB\(保有原來的MAC位址\)
  * 伺服器之間可以透過這片網路卡來相互溝通

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_13.png)

# 多點傳送模式(multicast mode)

* 每一台伺服器的網路卡仍然會保留原來的唯一MAC位址
  * 叢集成員之間仍然可以正常溝通
  * 交換器內每一個port所登記的是每台伺服器的唯一MAC位址
* 每一台伺服器還有一個共用的 __叢集__  __MAC__  __位址__  __\(__  __多點傳送__  __MAC__  __位址__  __\)__
* 叢集內所有伺服器都是隸屬於同一個 __多點傳送群組__ ，並透過上述 __多點傳送__  __\(__  __叢集__  __\)MAC__  __位址__ 來接聽要求

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_14.png)

# 多點傳送模式的缺點-1*1

* 路由器可能不支援
  * 例如路由器透過ARP通訊協定來詢找單點傳播位址220\.10\.11\.5的MAC位址，而它所獲得的卻是 __多點傳送__  __MAC__  __位址__ 01\-00\-5E\-7F\-0B\-05，有的路由器並不接受這樣的結果
* 解決方法
  * 可在路由器內建立靜態的ARP對照項目，以便將叢集IP位址對應到 __多點傳送__  __MAC__  __位址__
  * 若路由器不支援建立這類型的靜態資料的話，則可能需要更換路由器或改採用單點傳送模式

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_15.png)

* 仍有Switch Flooding問題
  * 例如路由器透過ARP通訊協定查詢220\.10\.11\.5的MAC位址時，所獲得是 __多點傳送__  __MAC__  __位址__ 01\-00\-5E\-7F\-0B\-05，然而交換器內並沒有任合一個port登記此MAC位址
* 解決方法
  * 如單點傳送模式所敘述
  * 可以選用支援 __IGMP snooping__ 的交換器，因為它們會窺探路由器與NLB叢集伺服器之間的IGMP封包\(加入群組、離開群組的封包\)，如此便可以得知哪一些port所連接的伺服器是隸屬於此多點傳送群組

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_16.png)

# 選擇單點或多點傳送模式

如果IIS網頁伺服器只有一片網路卡的話，請選用多點傳送模式

如果網頁伺服器擁有多片網路卡，或網路設備\(例如第2層交換器與路由器\)不支援多點傳送模式的話，可以採用單點傳播模式

# IIS 7的共用設定

* Web Farm內所有網頁伺服器的設定應該要同步
* 將網頁伺服器的設定檔儲存到遠端共用資料夾內，然後讓所有網頁伺服器都來使用這個設定檔
  * ApplicationHost\.config
    * 主要設定檔，儲存著伺服器內所有站台、應用程式、虛擬目錄與應用程式集區等設定。也儲存伺服器的通用預設值
  * Administration\.config
    * 儲存著委派管理的設定。也儲存IIS各模組的相關資料
  * ConfigEncKey\.key
    * 儲存所有伺服器相同的共用電腦金鑰，以便所有伺服器都能夠利用這些金鑰來加密、解密

# Web Farm實例演練圖

IIS網頁伺服器Web Farm、網址www\.sayms\.com

在兩台網頁伺服器上啟用Windows NLB

NLB操作模式為單點傳播模式

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_17.png)

# Windows NLB叢集的進階管理

刪除主機:將該伺服器從叢集中移除，並停用其 __網路負載平衡__ 功能

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_18.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_19.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_20.png)

# 連接埠規則

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_21.png)

* 叢集IP位址
  * 勾選 __全部__ 表示為 __通用連接埠規則__ ，其優先權最低
* 連接埠範圍
* 通訊協定

# 多重主機篩選模式

* 叢集依照親和性的設定來將要求交給叢集內的某台伺服器來負責處理
  * 每一台伺服器的負擔比率預設是相同的，若要變更單一伺服器的負擔比率的話，請針對該伺服器來設定
  * 對著伺服器按右鍵主機內容 __連接埠規則__ 標籤點選連接埠規則編輯

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_22.png)

# 單一主機篩選模式

與此規則有關的流量都將交給單一伺服器來負責處理，這台伺服器是 __處理優先順序__ \(handling priority\)較高的伺服器

__處理優先順序__ 預設就是根據host ID來設定\(數字較小優先順序越高\)。可變更伺服器的 __處理優先順序__ 值

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_23.png)

# 停用指定連接埠範圍的NLB

所有與此連接埠規則有關的流量都將被NLB叢集阻擋

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_24.png)

# 控制主機

啟動\(開始\)、停止、清空停止、暫停與繼續該台伺服器的服務

__停止__ :讓此伺服器停止處理所有的網路流量要求，包含正在處理中的要求

__清空停止__ \(drainstop\) :僅停止處理新的網路流量要求，但是目前正在處理中的要求並不會被停止

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_25.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_26.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch06-Web%20Farm%E8%88%87%E7%B6%B2%E8%B7%AF%E8%B2%A0%E8%BC%89%E5%B9%B3%E8%A1%A1%28NLB%29_27.png)

# 控制連接埠

啟用、停用或清空該連接埠規則

__停用__ :此伺服器不再處理與此規則有關的網路流量，包含正在處理中的要求

__清空__ \(drain\):僅停止處理新的網路流量要求，但目前正在處理中的要求並不會被停止

