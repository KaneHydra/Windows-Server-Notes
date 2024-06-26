第 17 章 建置iSCSI檔案伺服器容錯移轉叢集

17\-1 容錯移轉叢集概觀

17\-2 建立容錯移轉叢集實例演練

17\-3 在叢集中新增節點、移除節點與刪除叢集

# 17-1 容錯移轉叢集概觀

* 將數台伺服器組成 __容錯移轉叢集__ \(failover cluster\)
  * 它們之間會協同運作以提供一個高可用性的應用程式或服務環境
* 每一台伺服器被稱為一個 __節點__ \(node\)，節點之間透過網路硬體與軟體連接在一起來對使用者提供服務
* 容錯移轉\(failover\)
  * 若叢集之中有節點故障的話，其他節點會接手繼續提供服務
* FC SAN或iSCSI SAN架構的叢集

# FC SAN叢集

* 儲存裝置
  * 磁碟陣列\(disk array\)、光學磁碟櫃\(optical jukebox\)或磁帶庫\(tape library\)等
* Fiber Channel Protocol\(FCP\)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_0.png)

# Storage Area Network(SAN)

* SAN是用來將儲存裝置連接到伺服器的架構
* 伺服器會將SAN儲存裝置視為好像是直接連接在伺服器上
  * 例如伺服器會將SAN磁碟陣列內的一個磁碟區或一個邏輯單位號碼\(LUN\)視為一個硬碟

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_1.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_2.png)

\* NAS\(Network Attached Storage\)架構的儲存裝置:   伺服器是利用網路磁碟機來連接NAS儲存裝置\(在磁碟管理畫面內不   會多出一台硬碟\)。NAS架構所使用的通訊協定為NFS或SMB/CIFS

# iSCSI SAN叢集 - 1

Internet SCSI SAN 、 IP SAN

伺服器與儲存系統之間只需要透過IP網路就可以連接在一起

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_3.png)

* 連接儲存系統
  * iSCSI HBA介面卡
    * 內含iSCSI啟動器韌體
    * 可採用效率更好的 TOE iSCSI HBA介面卡
  * 1Gb或10Gb的Ethernet網路卡
    * 需要在節點上安裝iSCSI啟動器軟體

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_4.png)

* 1Gb或10Gb Ethernet交換器
* iSCSI Protocol
* 儲存系統
  * 內含目標 \(Target\)與儲存裝置
  * 伺服器先連接到目標，再透過目標來存取儲存裝置

---

TOE(TCP Offload Engine)功能的iSCSI HBA介面卡會將比較耗費CPU資源的處理工作改由介面卡來負責
為了能夠有良好的網路傳輸效率，因此應該採用高速的1Gb或 10Gb (或更高速)網路卡與switch

# 目標伺服器

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_5.png)

可用來取代儲存系統

例如Windows Server 2016 \+ Target軟體

* 儲存媒體
  * 目標伺服器內的PATA、SATA、SCSI、USB、FireWire等一般儲存媒體
  * 連接在這台伺服器上的磁碟陣列等儲存媒體

# 叢集的仲裁設定

* 仲裁設定\(quorum configuration\)
  * 用來決定可以容忍故障節點的數量，當太多節點故障時，叢集就會停止服務
  * Quorum\(法定數量\)：正常運作的節點數量達到法定數量的話，叢集就會繼續提供服務，否則就停止服務
* 在停止服務期間，正常節點會繼續監聽故障節點是否恢復正常，一旦正常節點數量回復到法定數量的話，叢集就會繼續提供服務
* __見證磁碟__ \(witness disk\)
  * 又稱 __仲裁磁碟__ \(quorum disk\)
  * 在計算法定數量時，有的仲裁設定會使用 __見證__  __磁碟__

# 仲裁設定的種類

* 節點多數
  * 不會使用 __見證__  __磁碟__
  * 適用於奇數節點叢集
  * 在正常節點數量佔多數的情況下，叢集才會提供服務
* 節點與磁碟多數
  * 適用於偶數節點叢集
  * 會將 __見證__  __磁碟__ 計算進來\(可將其視為1個節點\)
* 節點與檔案共用多數
  * 類似於 __節點__  __與磁碟__  __多數__ ，不過將 __見證__  __磁碟__ 改為 __見證__  __檔案__ \(共用資料夾內的檔案\)
* 沒有多數:只有磁碟
  * 若 __見證__  __磁碟__ 離線，叢集就會停止提供服務

\* 除了 __見證磁碟__ 與 __見證檔案__ 之外，Windows Server 2016也允許將 __見證__ 放到Microsoft Azure雲端\(需要Azure儲存帳號\)

# 叢集運作的基本原理

* 仍然正常運作的節點數量至少需要達到法定數量\(仲裁數量\)
* 仍然正常運作的節點數量需佔多數
  * 5節點的叢集：此時仍然正常運作的節點數量至少需要3個節點，因此 __仲裁數量__ 是3
  * 4節點的叢集：此時仍然正常運作的節點數量至少需要3個節點，因此 __仲裁數量__ 是3
  * 4節點\+1個 __見證磁碟__ 的叢集：可將其視為5個節點的叢集，此時仍然正常運作的節點數量至少需要3個節點

# 動態仲裁數量(dynamic quorum)

* 會自動調整 __仲裁__  __數量__
  * 5節點的叢集：原 __仲裁__  __數量__ 是3，若故障了3個節點、正常節點只剩下2個，應該要停止提供服務的，但 __動態仲裁__ 功能會將 __仲裁數量__ 自動由3降為2，讓正常節點數\(2\)仍然有達到 __仲裁數量__ \(2\)的要求，因此叢集會繼續提供服務
  * 4節點的叢集：原 __仲裁__  __數量__ 是3，若故障了2個節點，正常節點只剩下2個，應該要停止提供服務的，但 __動態__  __仲裁數量__ 功能將 __仲裁數量__ 自動由3降為2，讓正常節點數\(2\)仍然有達到 __仲裁數量__ \(2\)的要求，因此叢集會繼續提供服務

# 動態見證(dynamic witness)

* 若有 __見證__  __磁碟__ 的話，則系統會自動來判斷是否要將此 __見證磁碟__ 拿來計算 __仲裁數量__ \(是否擁有 __仲裁票__ 。叢集中的所有節點預設都擁有 __仲裁__  __票__ \)。
* 原則上是保持節點數量\(含 __見證磁碟__ \)為奇數，例如：
  * 4節點的叢集：此偶數節點叢集，原本的 __仲裁數量__ 是3，只允許1個節點故障，但是 __動態見證磁碟__ 功能會自動將 __見證磁碟__ 加入來計算 __仲裁數量__ ，因此變成5個節點， __仲裁數量__ 仍然是3，可以允許2節點故障
  * 5節點的叢集：此奇數節點叢集的 __仲裁數量__ 是3，允許2個節點故障，不需要將 __見證磁碟__ 加入來計算 __仲裁數量__

# 查看動態仲裁與動態見證

系統預設會啟用動態仲裁數量與動態見證，可以利用Windows PowerShell指令來查看

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_6.png)

# 17-2 建立容錯移轉叢集實例演練

* 需要Active Directory網域，假設網域名稱為sayms\.local
  * 伺服器DC為網域控制站兼DNS伺服器
* 叢集中的節點伺服器需隸屬於同一個網域，且應該要扮演同樣的網域角色，也就是都是成員伺服器\(建議\)或都是網域控制站

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_7.png)

目標伺服器不需要加入網域，其內將安裝Windows Server 2016內建的 __iSCSI__  __目標伺服器__ 軟體

---

圖中網域控制站、兩個節點與目標伺服器都採用Windows Server 2016 Datacenter

# 軟硬體要求

* 應該都要配備相同或類似的硬體
* 需要安裝相同版本的作業系統
  * 也要安裝相同的Service Pack與軟體更新\(update\)
* 在Windows Server 2016 系列產品中只有Datacenter與Standard版本才具備叢集功能

# 網路設定

* public網路
  * 網域控制站、用戶端、節點間相互溝通的網路
* private網路
  * 兩個節點用來監聽對方“心跳”的專屬網路
  * 將public網路設定為監聽對方“心跳”的備援網路
* iSCSI網路
  * 兩個節點透過此網路來連接目標伺服器與存取儲存媒體內的檔案
  * 此網路應僅用於節點與目標伺服器之間使用iSCSI protocol來溝通的專用網路，勿作為其他用途
  * 兩個節點用來連接iSCSI網路的網路卡應該要相同，而且應使用1Gb、10Gb或更高速的網路卡與交換器

---

實體電腦的話，可以直接利用電腦對電腦的crossover網路線(跳線)來串接這兩個節點


# 可加強其他的容錯措施

* 為了避免單一點故障而影響到叢集運作，建議在電腦之間的溝通管道上採取適當容錯措施，例如兩個節點與用戶端之間可以：
  * 在節點利用兩片網路卡來連接兩個網路，而透過這兩個網路都可以跟用戶端溝通
  * 採用具備teaming功能的網路卡來提供容錯功能
* Teaming
  * 將一台電腦內多片實體網路卡，透過驅動程式變成一片虛擬網路卡，其他電腦透過此虛擬網路卡來與此電腦溝通，但資料實際上是透過多片實體網路卡來傳送
  * 可提高網路傳送速度、提供負載平衡與容錯功能
  * Windows Server 2016已內建網路卡的teaming驅動程式。此功能在Windows Server 2016內被稱為NIC Teaming\(NIC小組\)
  * 不建議Private網路使用teaming，以免因為延遲問題而影響到節點之間溝通的即時性
  * iSCSI不支援teaming功能，因此iSCSI網路不要使用teaming

# 目標伺服器與儲存媒體

* 仲裁設定為 __節點與磁碟多數__
  * 需要使用檔案磁碟與見證磁碟
    * 需基本磁碟，不可以是動態磁碟
    * 見證磁碟可為NTFS或ReFS 、檔案磁碟建議為NTFS
    * 可以是MBR磁碟或GPT磁碟
  * 本範例的見證磁碟與檔案磁碟都直接使用本機電腦C:磁碟內的檔案來模擬，分別是C:\\iSCSIVirtualDisks\\Quorum\.vhd與C:\\iSCSIVirtualDisks\\Files\.vhd

# iSCSI SAN兩節點檔案伺服器叢集實例演練

* 準備網路環境與電腦
* 適當的節點設定
* 目標伺服器與儲存媒體的設定
* 讓節點伺服器可以連接iSCSI虛擬磁碟
* 在節點伺服器安裝「容錯移轉叢集」功能
* 驗證叢集設定
* 建立叢集
* 設定兩節點的檔案伺服器容錯移轉叢集
  * 設定叢集網路的用途
  * 建立與測試兩節點的檔案伺服器容錯移轉叢集
    * 在Windows防火牆開放所需流量
    * 建立檔案伺服器容錯移轉叢集

# 17-3 在叢集中新增節點、移除節點與刪除叢集

* 到新節點伺服器上完成所有設定
  * 安裝所需的3片網路卡、安裝作業系統、設定各網路卡的IP位址、關閉 __Windows__  __防火牆__ 、測試與其他伺服器之間是否溝通正常、加入網域、重新開啟 __Windows__  __防火牆__ 、調整TCP/IP設定、到目標伺服器Target上設定讓新節點的iSCSI啟動器可以來連接目標quorum與files、回到新節點伺服器繼續設定讓iSCSI啟動器來連接上述2個目標、安裝 __容錯移轉叢集__ 功能、在 __Windows__  __防火牆__ 開放 __網域網路__ 位置的 __遠端磁碟區管理__ 流量等
* 新增節點

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_8.png)

# 移除節點

若該節點是檔案伺服器的擁有者的話，請先將擁有者轉移到其他節點

從叢集中撤出

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_9.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_10.png)

# 刪除叢集

先移除叢集內的角色\(檔案伺服器MyClusterFS\)

摧毀叢集

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_11.png)

![](WS2016%E7%B3%BB%E7%B5%B1%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA237-Ch17-%E5%BB%BA%E7%BD%AEiSCSI%E6%AA%94%E6%A1%88%E4%BC%BA%E6%9C%8D%E5%99%A8%E5%AE%B9%E9%8C%AF%E7%A7%BB%E8%BD%89%E5%8F%A2%E9%9B%86_12.png)

