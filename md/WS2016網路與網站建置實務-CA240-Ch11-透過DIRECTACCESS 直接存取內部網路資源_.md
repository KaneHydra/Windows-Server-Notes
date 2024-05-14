第 11 章 透過DirectAccess直接存取內部網路資源

11\-1 DirectAccess概觀

11\-2 DirectAccess實例演練之1－內部網路僅含IPv4主機

11\-3 DirectAccess實例演練之2－用戶端位於NAT之後

11\-4 DirectAccess實例演練之3－內部網路含IPv4與IPv6主機

# DirectAccess概觀

* 出門在外的使用者若要存取公司內部網路資源的話，傳統上大都是透過 __虛擬私人__  __網路__ \(VPN\)，然而對使用者來說，虛擬私人網路的使用較為複雜與不方便
* DirectAccess讓使用者的電腦在外只要能夠連上網際網路，不需要再執行其他額外步驟，便能夠自動與內部網路建立起雙向溝通管道，讓使用者可以很容易的存取內部網路資源
  * 系統管理員也可以來管理這些在外部的電腦

# DirectAccess的運作原理

* DirectAccess用戶端在連接DirectAccess伺服器時，會與伺服器建立兩個雙向的IPsec通道
  * __基礎結構通道__ ：用戶端透過此通道來連接網域控制站、DNS伺服器等基礎結構伺服器
  * __內部網路通道__ ：用戶端透過此通道來連接內部網路的資源伺服器，例如檔案伺服器、網站與其他應用程式伺服器等
* 用戶端的網路位置有變化時，它會自動偵測其所連接的網路，若是網際網路的話，它就會採用DirectAccess方式來連接DirectAccess伺服器

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_0.png)

# DirectAccess的功能與特色 - 1

* DirectAccess與RRAS管理工具整合在一起
* DirectAccess需使用IPv6
  * 網際網路的用戶端使用IPv6來連接DirectAccess伺服器，然後透過它來連接內部網路的IPv6或IPv4主機
  * 若用戶端與伺服器之間為IPv4網路的話，可以使用6to4或Teredo來將IPv6流量封裝到IPv4封包內、透過IPv4網路將其傳給DirectAccess伺服器
    * 若用戶端的public IPv4位址：使用6to4\(若6to4被停用，則改用Teredo\)
    * 若用戶端的private IPv4位址：使用Teredo
  * 若防火牆阻擋，以致使用6to4或Teredo無法連接到DirectAccess伺服器的話，則用戶端會嘗試改用IP\-HTTPS

* DirectAccess伺服器的網路介面要求
  * DirectAccess伺服器需要2個網路介面，一個用來連接內部網路，另一個用來連接網際網路
  * 網際網路介面需要2個連續的public IP位址\( __Teredo__  __伺服器__ 角色\)，例如140\.115\.8\.3、140\.115\.8\.4
  * 若DirectAccess伺服器位於NAT設備之後，則只需要一個網路介面，且可以不需要使用public IP位址
* 用戶端與伺服器之間採用IPsec來驗證雙方身分，此時若是選用電腦憑證驗證方式、且DirectAccess伺服器有2個網路介面的話，就會建立如前所述的2個通道\( __基礎__  __結構通道__ 與 __內部網路__  __通道__ \)
  * 若透過 __快速入門__  __精靈__ 的話，則只會建立一個IPsec通道。此時雙方會被設定為使用Kerberos驗證方式\(含電腦驗證與使用者驗證\)，而且會在DirectAccess伺服器啟用 __Kerberos __  __Proxy__

* DirectAccess用戶端可連接內部IPv6與IPv4主機
  * 當DirectAccess用戶端以IPv6方式，將連接內部主機的要求傳送到DirectAccess伺服器後：
    * 若此內部主機為IPv6主機的話，則DirectAccess伺服器會以IPv6方式來連接內部IPv6主機
    * 若此內部主機為IPv4主機的話，則DirectAccess伺服器會以IPv4方式來連接內部IPv4主機

* 內建NAT64與DNS64來支援存取IPv4主機的資源
  * 讓使用IPv6的DirectAccess用戶端，可以連接僅支援IPv4的主機
* NAT64可將IPv6封包轉換為IPv4封包、將IPv4封包轉換為IPv6封包
* DNS64可將IPv4 A記錄轉換為IPv6 AAAA記錄
* 步驟\(2\)會同時查詢主機fs1\.sayms\.local的AAAA與A記錄後，若DNS伺服器有回應AAAA IPv6位址的話，表示主機fs1\.sayms\.local支援IPv6，則DNS64會直接回應此IPv6位址給用戶端，此時用戶端與這台IPv6主機溝通時，並不需要NAT64的轉換處理

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_1.png)

* 名稱解析原則表格\(NRPT\)
  * 用戶端透過NRPT來判斷是否要將查詢內部主機IP位址的要求傳送給DirectAccess伺服器
  * 在設定DirectAccess伺服器時，便會設定NRPT表格的規則內容，然後透過網域群組原則來將設定套用到用戶端電腦
* 當用戶端要查詢尾碼為\.sayms\.local的主機時\(例如fs1\.sayms\.local\)，它會向IPv6位址為2002:8c73:803:3333::1的DNS伺服器來查詢，此伺服器就是扮演DNS64角色的DirectAccess伺服器
* 若用戶端欲查詢的主機名稱或其尾碼未列在表格中的話，例如www\.microsoft\.com，則會向一般DNS伺服器來查詢，此DNS伺服器可能是透過DHCP伺服器來設定的、或是網路介面的TCP/IP處的 __慣用__  __DNS__  __伺服器__
* 若用戶端欲查詢的主機名稱或其尾碼有列在表格中，但卻未指定DNS伺服器的話，例如圖中的DirectAccess\-NLS\.sayms\.local，則用戶端也會向一般DNS伺服器來查詢

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_2.png)

* 網路位置伺服器\(Network Location Server，NLS\)
  * 當用戶端的網路位置有變化時，其Network Location Awareness服務會自動偵測其所連接的網路，若用戶端“不是”連接到內部網路的話，才會使用DirectAccess
  * 需在內部網路架設一台 __網路位置__  __伺服器__
    * 若用戶端可以透過https連接到 __網路位置伺服器__ 的話，就表示用戶端是位於內部網路，否則就是位於網際網路，此時就會採用DirectAccess方式來連接DirectAccess伺服器
    * 需確保用戶端在內部網路可以利用https連接到 __網路位置__  __伺服器__ ，否則用戶端會誤認為其在網際網路，因而可能無法存取內部網路資源

* 圖中假設 __網路位置伺服器__ 是由DirectAccess伺服器兼任，您也可以使用另外一台伺服器來扮演 __網路位置__  __伺服器__
* 圖中網際網路的用戶端可以連接到DirectAccess伺服器，但卻無法連接到由DirectAccess伺服器所扮演的 __網路位置__  __伺服器__
  * __網路__  __位置伺服器__ 的FQDN為DirectAccess\-NLS\.sayms\.local，雖然在用戶端的NRPT中有此FQDN，但並未指定DNS伺服器，因此會透過一般DNS伺服器來查詢其IP位址，因無法解析到IP位址，故無法連接到 __網路位置伺服器__ ，因此用戶端會正確的認為其在網際網路，故會採用DirectAccess方式

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_3.png)

---

您應該要確保用戶端在網際網路無法解析到DirectAccess-NLS.sayms.local的IP位址

__與__  __NAP__  __整合__ ：用戶端連接到DirectAccess伺服器後，可以透過 __網路存取__  __保護__ \(NAP\)來檢查用戶端的安全設定是否符合要求，DirectAccess伺服器可以拒絕不符合要求的用戶端來存取內部網路的資源

__支援負載平衡__ ：可透過 __Windows__  __網路負載平衡__ 來提供高可用性的運作環境

__支援多重網域__ ：允許不同網域的用戶端來連線

__支援多重站台__ ：若在多個站台\(site\)建置DirectAccess伺服器，則用戶端可自動選擇最近的DirectAccess伺服器來連線、或設定來連接喜好的DirectAccess伺服器、或讓使用者自行選擇欲連線的DirectAccess伺服器

__支援強制__  __通道__  __\(force tunneling\)__ ：也就是強迫DirectAccess用戶端存取網際網路資源的流量，都必須先透過通道傳給DirectAccess伺服器，再透過DirectAccess來傳送到網際網路

__DirectAccess__  __用戶端的要求__ ：用戶端需為Windows 10 Enterprise、Windows 8\.1\(8\) Enterprise、Windows 7 Enterprise/Ultimate，且必須加入網域，中以Windows 10 、Windows 8\.1\(8\)的支援性最完整。其他用戶端可透過傳統的VPN方式來存取內部網路資源。 Windows 7用戶端僅能連接指定的DirectAccess 伺服器，無法自動選擇最近的DirectAccess伺服器來連線。

---

其中以Windows 8.1(8)的支援性最完整，其他用戶端則有些功能不支援，例如若在多個站台(site)建置DirectAccess伺服器的話，則Windows 7用戶端僅能連接指定的DirectAccess 伺服器，無法自動選擇最近的DirectAccess伺服器來連線

# DirectAccess實例演練之1－內部網路僅含IPv4主機

* DC1：網域控制站、DNS伺服器\(支援AD與對內部用戶端提供名稱解析服務\)、DHCP伺服器\(指派IP位址、DNS伺服器與預設閘道等給內部用戶端\)
* APP1：提供網頁與檔案資源的伺服器\(網域成員伺服器\)
* Win10PC1：加入網域的DirectAccess用戶端
  * 先放置到 __內部網路__ ，之後會被搬移到外部 __測試網路__ 來測試是否可連接到DirectAccess伺服器、存取位於內部網路的APP1內的資源
* DA1：DirectAccess伺服器\(網域成員伺服器\)

* SERVER1：用來模擬網際網路的DHCP伺服器與DNS伺服器
  * DHCP伺服器用來指派IP位址、DNS伺服器與預設閘道給 __測試網路__ 內的用戶端Win10PC1
  * DNS伺服器用來對用戶端Win10PC1提供名稱解析服務

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_4.png)

# 準備好測試環境中的網路環境

* 用戶端Win10PC1必須能夠連接網際網路，其DirectAccess功能才會正常，由於我們要在圖中的 __測試網路__ 來測試DirectAccess，因此圖中的 __測試__  __網路__ 需可連接到網際網路
* DirectAccess伺服器用來連接圖中網際網路\( __測試網路__ \)的網路介面\(外網卡\)需要2個連續的public IP位址
  * 在圖中借用了2個public IP位址140\.115\.8\.3、140\.115\.8\.4
  * 利用圖左邊的NAT來隱藏這2個public IP位址

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_5.png)

# 利用Microsoft Hyper-V建置網路環境

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_6.png)

* 利用Hyper\-V主機的 __網際網路連線__  __共用__ \(ICS\)來提供NAT功能
* 需將ICS的 __DHCP__  __配置器__ 停用，以免干擾到SERVER1的DHCP伺服器
  * 將 __vEthernet__  __\(__  __測試__  __網路虛擬__  __交換器__  __\)__ 的IP位址設定為非DHCP指派的範圍（192\.168\.137\.0/24\)後，例如圖中的140\.115\.8\.254/24，它就會自動停用 __DHCP__  __配置__  __器__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_7.png)

# 利用VMware Workstation建置網路環境

圖中選擇VMnet2虛擬網路來當作 __內部__  __網路__

利用VMware內建的NAT:需將其DHCP服務停用，以免干擾到SERVER1的DHCP伺服器

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_8.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_9.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_10.png)

# 利用ORACLE VirtualBox建置網路環境

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_11.png)

* 利用Hyper\-V主機的 __網際網路連線共用__ \(ICS\)來提供NAT功能
* 需將ICS的 __DHCP__  __配置器__ 停用，以免干擾到SERVER1的DHCP伺服器
  * 將 __VirtualBox Host\-Only Network__ 的IP位址設定為非DHCP指派的範圍（192\.168\.137\.0/24\)後，例如圖中的140\.115\.8\.254/24，它就會自動停用 __DHCP__  __配置__  __器__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_12.png)

# 準備好測試環境中的電腦

DC1、APP1、DA1與SERVER1都是Windows Server 2016 Enterprise、Win10PC1是Windows 8\.1 Enterprise

請先將每一台電腦的作業系統安裝完成（複製的虛擬機器或硬碟，需執行Sysprep\.exe且需勾選 __一般化__ ）、電腦名稱與IP位址等都依照圖所示設定完成，注意DA1的外網卡有2個IP位址

利用ping指令來確認電腦之間可以正常溝通\(注意 __Windows__  __防火牆__ \)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_13.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_14.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_15.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_16.png)

# 網域控制站的安裝與設定

* 透過 __伺服器管理員__ 來新增 __Active __  __Directory__  __網域__  __服務__ ，假設網域名稱為sayms\.local，完成後重新啟動DC1、以系統管理員身分登入
* 繼續在DC1上安裝DHCP伺服器角色
  * 建立IPv4位址領域：假設領域名稱為 __內部網路__ 、領域範圍為192\.168\.8\.50到192\.168\.8\.150；同時設定DHCP選項，包含父系網域名稱為sayms\.local、DNS伺服器IP位址為192\.168\.8\.1

# 資源伺服器APP1的設定

* 加入網域
* 安裝 __網頁伺服器（__  __IIS__  __）__
* 建立一個供測試用的共用資料夾
  * 在此資料夾內隨意新增一個文字檔

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_17.png)

# DirectAccess用戶端Win10PC1的設定

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_18.png)

* 確認已經成功從DHCP伺服器取得設定
* 加入網域
* 確認可以連接到扮演檔案伺服器角色的APP1的共用資料夾
  * \\\\APP1\\TestFolder
* 確認可以連接到同時扮演網站角色的APP1內的網頁
  * http://app1\.sayms\.local/

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_19.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_20.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_21.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_22.png)

# 在DC1針對DirectAccess用戶端建立安全性群組

將用戶端Win10PC1加入此群組內，之後在伺服器DA1上啟用DirectAccess伺服器功能時，會將DirectAccess相關的設定，透過群組原則套用到此群

將DirectAccess伺服器加入網域

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_23.png)

# DNS與DHCP伺服器SERVER1的設定

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_24.png)

* 需安裝DNS伺服器與安裝DHCP伺服器角色
* DirectAccess用戶端Win10PC1在網際網路\( __測試網路__ \)上利用主機名稱da1\.sayda\.com來連接DirectAccess伺服器
  * 需將其解析到DirectAccess伺服器外網卡的IP位址140\.115\.8\.3
  * 建立sayda\.com主要區域、建立da1主機的A記錄
* 建立IPv4位址領域：假設領域名稱為 __測試網路__ 、領域範圍為140\.115\.8\.50到140\.115\.8\.150、子網路遮罩255\.255\.255\.0；設定DHCP選項，包含 __路由__  __器__ \(預設閘道\)的IP位址為140\.115\.8\.254\(NAT的IP位址\)、DNS伺服器IP位址為140\.115\.8\.5

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_25.png)

# 用戶端Win10PC1的基本網路功能測試

* 本環境並未讓 __內部網路__ 可連接網際網路
  * 將滑鼠指標移到桌面畫面右下角的網路圖示，它會顯示 __無網際網路存取__ 訊息
  * 利用ping指令測試\(例如ping 8\.8\.8\.8\)，並無法得到對方的回應
* 將Win10PC1電腦搬移到 __測試網路__ 內，它會從DHCP伺服器\(SERVER1\)取得IP位址與DNS伺服器等選項，畫面右方也會出現 __網路__ 的提示訊息，將滑鼠指標移到桌面畫面右下角的 __網路__ 圖示，它會顯示 __網際網路存取__ 訊息
* 因為伺服器DA1的DirectAccess伺服器功能尚未啟用，故此時用戶端Win10PC1無法透過DA1來存取內部網路的資源，因此利用 __\\\\APP1\\TestFolder__ 與 __http://app1\.sayms\.local__  __/__ 都無法連線成功
* 完成以上測試後，將用戶端電腦Win10PC1搬回 __內部網路__

位於 __測試網路__ 的用戶端Win10PC1應該可以透過DNS伺服器\(SERVER1\)來解析到da1\.sayda\.com的IP位址

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_26.png)

---

可點擊此畫面中的是，開啟共用並連線到裝置
必要時請先清除瀏覽器的快取資料

# 在DirectAccess伺服器DA1安裝與設定「遠端存取」 - 1

安裝 __遠端存取__ 角色、透過 __快速入門精靈__ 來將其設定為DirectAccess伺服器

__快速入門精靈__ 讓用戶端與伺服器之間只建立一個IPsec通道、將DirectAccess伺服器DA1設定為Kerberos Proxy、雙方之間在IPsec通道中使用Kerberos驗證、DA1使用自我簽署憑證來接收用戶端的https流量

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_27.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_28.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_29.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_30.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_31.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_32.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_33.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_34.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_35.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_36.png)

---

若要變更設定的話，可透過圖中4個步驟中的編輯鈕

若 __Windows__  __防火牆__ 被停用，或 __網域設定檔__ 與 __公用設定檔__ 之一被停用的話 ，DirectAccess功能將無法正常運作

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_37.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_38.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_39.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_40.png)

精靈讓用戶端與伺服器之間僅建立一個IPsec通道

# 將用戶端Win10PC1搬移到內部網路來套用群組原則設定

* 待會將用戶端Win10PC1搬移到網際網路\( __測試網路__ \)後，其將獲得public IP，因此會採用6to4
* 若使用6to4無法連線成功，且無法順利自動改用Teredo或IP\-HTTPS的情況的話：
  * 請透過PowerShell指令netsh interface 6to4 set state disabled將6to4停用，以便直接改用Teredo或IP\-HTTPS。也可以利用netsh interface 6to4 show state指令來查看目前6to4的啟用狀態

  * 透過GPO「 __DirectAccess__  __用戶端設定__ 」
    * 電腦設定原則系統管理範本網路TCPIP設定值IPv6轉換技術雙擊 __設定__  __6to4__  __狀態__ 點選 __已啟用__ 在 __從下列狀態中選取__ 選項中選擇 __停用狀態__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_41.png)

\* 若要將Teredo停用或啟用的話： __netsh__  __ interface __  __teredo__  __ set state __  __disabled__  __、__  __netsh__  __ interface __  __teredo__  __ set state client__

---

即使6to4的功能都正常，然而目前電信廠商一般並未開放6to4的流量（通訊協定號碼為41），因此DirectAccess用戶端可能還是無法透過6to4來連接DirectAccess伺服器

# 檢查用戶端Win10PC1的DirectAccess設定 - 1

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_42.png)

* \.sayms\.local規則
  * 2002:8c73:803:3333::1扮演DNS64角色的DirectAccess伺服器
* DirectAccess\-NLS\.sayms\.local規則
  * 它是 __網路位置伺服器__ 的主機名稱，用戶端在網際網路上欲查詢DirectAccess\-NLS\.sayms\.local的IP位址時，會向一般DNS伺服器SERVER1來查詢，但並無法查詢到，因此用戶端無法與 __網路位置伺服器__ 溝通，便據以判斷它是位於網際網路
  * 此DNS伺服器是透過DHCP伺服器SERVER1的選項來指定的
  * 若用戶端在網際網路上欲查詢的主機名稱或其尾碼未列在表格中的話，則它也會向一般DNS伺服器\(SERVER1\)來查詢

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_43.png)

---

或使用Get-DnsClientNrptPolicy。若未看到這些規則的話，請確認您的Windows 8.1為Enterprrise版且已經套用原則
可以透過【點擊步驟2（遠端存取伺服器）的編輯鈕點擊網路介面卡】來查看DNS64的IPv6位址
若要在DirectAccess伺服器上查看或修改以上3個規則的話：【點擊步驟3（基礎結構伺服器）的編輯鈕點擊DNS】

* Win10PC1目前是在內部網路，故以上NRPT規則無效
* 可以透過 __Get\-__  __NCSIPolicyConfiguration__ 指令來查看 __網路連線狀態__  __指示器__ \(NCSI\)的相關設定
  * 用戶端透過URL  __https://__  __DirectAccess\-NLS\.sayms\.local:443/insideoutside__ 來連接 __網路位置__  __伺服器__
  * DirectAccess伺服器需要憑證\(目前使用自我簽署憑證\)。若用戶端可以透過此URL來連接 __網路位置伺服器__ 的話，便表示用戶端是位於內部網路

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_44.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_45.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_46.png)

ConnectLocally表示用戶端在內部網路

用戶端的Network Connectivity Assistant服務需已啟動

---

NCSI：Network Connectivity Status Indicator
若用戶端的Network Connectivity Assistant服務未啟動的話，則執行Get-DAConnectionStatus指令會有錯誤警示

# 將用戶端Win10PC1搬移到網際網路來測試DirectAccess - 1

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_47.png)

Win10PC1搬移到網際網路\( __測試網路__ \)後，會從DHCP伺服器\(SERVER1\)取得IP位址與DNS伺服器等選項設定值，此時將滑鼠指標移到桌面畫面右下角的 __網路__ 圖示，它會顯示 __網際網路存取__ 訊息

用戶端無法透過 __測試網路__ 中的DNS伺服器SERVER1解析到 __網路位置伺服器__ DirectAccess\-NLS\.sayms\.local的IP位址，因此無法連接到 __網路位置伺服器__ ，此時用戶端會認為其位於網際網路，故NRPT規則便有效

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_48.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_49.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_50.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_51.png)

IPv6位址：由DirectAccess伺服器的DNS64自行利用 __動態首碼 __ \+  __伺服器__  __APP1__  __的__  __IPv4__  __位址__ 所組合成的

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_52.png)

http://app1\.sayms\.local/

\\\\APP1\\TestFolder

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_53.png)

* Get\-NetIPAddress指令來查看用戶端IPv6位址資訊
* Get\-NetIPHTTPSConfiguration
  * 用戶端是被設定為利用https://da1\.sayda\.com:443/IPHTTPS來連接DirectAccess伺服器

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_54.png)

DirectAccess用戶端與伺服器之間所建立的IPsec SA

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_55.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_56.png)

到DirectAccess伺服器來查看DirectAccess用戶端的連線狀態

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_57.png)

---

DirectAccess用戶端與伺服器之間是利用IPsec來驗證電腦與使用者
本演練環境還需要供下一個演練來使用，故暫時不要破壞它，建議利用虛擬軟體的快照功能（snapshot）將這些虛擬機器狀態儲存起來

# DirectAccess問題與解決方法

| Get-DAConnectionStatus出現以下訊息 | 可能原因與可嘗試的解決方法 |
| :-: | :-: |
| Status:ActionableErrorSubstatus:InternetConnectivityDown | 用戶端無法上網。請檢查 測試網路 是否可上網、用戶端的TCP/IP組態是否正確、SERVER1的DHCP伺服器所指派的IP位址與選項是否正確 |
| Status:Error Substatus:NameResolutionFailure | DirectAccess伺服器的Windows防火牆是否有開啟、網域設定檔與公用設定檔是否都是作用中 |
| Status:ErrorSubstatus:RemoteNetworkAuthenticationFailure | 將用戶端的網路卡停用再重新啟用 |
| Status:Error Substatus:CouldNotContactDirectAccessServer | 圖 11-2‑25中所設定的主機名稱(da1.sayda.com)是否與DNS 伺服器 (SERVER1) 內的主機記錄相同、DNS 伺服器內的da1.sayda.com的IP位址是否正確、用戶端從DHCP伺服器租到的IP位址、子網路遮罩是否正確 |
| Network Connectivity Assistant服務未啟動 | 手動啟動此服務 |

* 若用戶端的NRPT損毀，例如用戶端實際上是在內部網路，但是執行netsh namespace show effectivepolicy指令時，卻顯示了2個有效規則，以至於用戶端無法與內部網路的電腦溝通。解決方法：
  * 執行REGEDIT\.EXE
  * 刪除位於以下路徑的子機碼DnsPolicyConfig
    * HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Policies\\Microsoft\\Windows NT\\DNSClient
  * 執行net stop dnscache、net start dnscache、gpupdate /force

\* 也可以【按   \+ I鍵網路和網際網路DirectAccess】來查看連線狀態

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_58.png)

# DirectAccess實例演練之2－用戶端位於NAT之後

* 用戶端在大部分的情況下­是使用private IP
  * 例如公司員工拜訪客戶時，其所獲得的IP位址一般都是private IP
  * 又例如員工在家裡若使用IP分享器的話，其所獲得的也會是private IP位址
* 直接沿用上一節的測試環境圖，但多了一個 __客戶網路__ 與一台扮演NAT角色的電腦NAT1

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_59.png)

# NAT1的ICS設定

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_60.png)

NAT1為Windows 10電腦

透過 __網際網路連線共用__ \(ICS\)來提供NAT功能

* 擁有2片網路卡
  * 外網卡用來連接 __測試網路__ ，其IP位址採用自動取得即可，它會向扮演DHCP伺服器角色的SERVER1來索取IP位址
  * 內網卡用來連接 __客戶網路__ ，其IP位址也不需手動設定\(啟用ICS功能後，系統會自動設定其IP位址\)
  * 建議將這兩片卡的連線名稱分別改為如圖所示的 __客戶網路__ 與 __測試__  __網路__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_61.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_62.png)

# 用戶端位於NAT之後的測試 - 1

將用戶端電腦Win10PC1搬移到 __客戶__  __網路__

用戶端無法透過 __客戶網路__ 中的DNS伺服器\(NAT1的 __DNS__  __轉接__  __代理__ \)解析到 __網路位置伺服器__ DirectAccess\-NLS\.sayms\.local的IP位址，因此用戶端會認為其“不是”位於內部網路，故NRPT規則便有效

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_63.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_64.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_65.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_66.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_67.png)

IPv6位址：由DirectAccess伺服器的DNS64自行利用 __動態首碼 __ \+  __伺服器__  __APP1__  __的__  __IPv4__  __位址__ 所組合成的

http://app1\.sayms\.local/

\\\\APP1\\TestFolder

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_68.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_69.png)

* 可以利用 __Get\-__  __NetIPAddress__ 指令來查看用戶端的IPv6位址資訊
* __Get\-__  __NetIPHTTPSConfiguration__ ：
  * 用戶端是被設定為利用https://da1\.sayda\.com:443/IPHTTPS來連接DirectAccess伺服器

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_70.png)

\* 雖然目前用戶端使用private IP位址\(192\.167\.137\.0/24\)，應該使用Teredo，但是還需要更   多的其他設定，故用戶端的Teredo連線會失敗而改用IP\-HTTPS

DirectAccess用戶端與伺服器之間所建立的IPsec SA

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_71.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_72.png)

到DirectAccess伺服器來查看DirectAccess用戶端的連線狀態

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_73.png)

---

DirectAccess用戶端與伺服器之間是利用IPsec來驗證電腦與使用者
本演練環境還需要供下一個演練來使用，故暫時不要破壞它，建議利用虛擬軟體的快照功能（snapshot）將這些虛擬機器狀態儲存起來

# DirectAccess實例演練之3－內部網路含IPv4與IPv6主機

用戶端在網際網路\( __測試網路__ \)將使用IP\-HTTPS、在 __客戶__  __網路__ \(扮演NAT角色的電腦NAT1之後\)將使用Teredo來連接DirectAccess伺服器

演練圖是延續前一章節的環境，假設您已經完成前一章節的演練

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_74.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_75.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_76.png)

# 準備好測試環境中的電腦 - 1

先將DirectAccess用戶端Win10PC1搬移回 __內部__  __網路__

移除前一章節的DirectAccess伺服器設定

參考演練圖來增加設定IPv6與IPv4組態

* 增加用來扮演IPv4主機角色的APP2電腦
  * 安裝Windows Server 2016、將電腦名稱設定為APP2、加入網域
  * 將其IPv6停用來模擬成僅支援IPv4的電腦。上網搜尋關鍵字「如何停用 Windows 中的 IPv6 或其元件」來到Microsoft網站下載「停用 IPv6」的檔案、透過執行此檔案來將此台Windows Server 2016的IPv6停用\(完成後重新啟動電腦\)
  * 您也可以透過在以下的登錄路徑將DisabledComponents值設定為0xff的方式來停用IPv6：
    * HKEY\_LOCAL\_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\Tcpip6\\Parameters\\
    * 若不存在DisabledComponents的話，請自行新增之，其資料類型為DWORD （32\-位元） 。若將其值設定為0 的話，可重新啟用所有 IPv6 元件
* 增加CA：https由自我簽屬憑證改為向CA申請、IPsec通道改為電腦憑證驗證\(原使用Kerberos驗證\)
* __網路位置伺服器__ 改由伺服器APP1扮演\(原由DirectAccess伺服器兼任\)

# 網域控制站DC1的設定

* 除了前一章節的設定之外，DC1還需要增加以下的設定
  * 安裝憑證授權單位（CA）角色
  * 在DNS伺服器內新增「網路位置伺服器 __」__ 的主機記錄
  * 透過群組原則來開放ICMPv4與ICMPv6「回應要求」流量
  * 變更「Web伺服器」憑證範本設定
  * 透過群組原則來讓內部電腦自動安裝IPsec所需的憑證
* 安裝憑證授權單位（CA）角色

* 在DNS伺服器內新增「網路位置伺服器」的主機紀錄
  * 讓DirectAccess用戶端在內部網路時，可以解析到 __網路位置伺服器__ APP1的IP位址

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_77.png)

# 使用Teredo需開放ICMPv4與ICMPv6流量

位於NAT之後的用戶端若使用Teredo來連接DirectAccess伺服器的話，會先連接到Teredo伺服器\(由DirectAccess伺服器扮演\)，再根據Teredo伺服器的回應來決定欲連接的Teredo Relay\(也是由DirectAccess伺服器扮演\)，最後用戶端會與Teredo Relay建立Teredo通道，並透過此通道來與內部主機溝通

使用Teredo的用戶端為了確認可以連接到目的地主機，該用戶端會使用ICMPv6來與目的地主機溝通，因此用戶端所欲溝通的所有內部主機的 __Windows__  __防火牆__ 都需開放ICMPv6流量

若用戶端是透過DNS64/NAT64機制來存取內部IPv4主機資源的話，則這些主機的 __Windows__  __防火牆__ 也都需開放ICMPv4的流量

用戶端上述流量是送到DirectAccess伺服器來傳送，因此DirectAccess伺服器的 __Windows__  __防火牆__ 也需開放ICMPv4與ICMPv6流量

使用Teredo 的用戶端是透過UDP連接埠號碼3544來連接DirectAccess伺服器，因此若用戶端與伺服器之間被防火牆隔開的話，防火牆需開放UDP 3544，開放方向為用戶端到伺服器

---

位於NAT之後的DirectAccess用戶端可以使用Teredo或IP-HTTPS來連接DirectAccess伺服器

# 透過群組原則來開放ICMPv4與ICMPv6回應要求流量

可透過Default Domain Policy來讓內部所有主機都開放ICMP連入流量

展開 __電腦設定__ 原則Windows設定安全性設定…

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_78.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_79.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_80.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_81.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_82.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_83.png)

# 所需申請的憑證

* DirectAccess用戶端透過 __https://da1\.sayda\.com:443/IPHTTPS__ 來連接DirectAccess伺服器，因此DirectAccess伺服器DA1需要一個主體名稱為da1\.sayda\.com的 __伺服器驗證__ 憑證
* DirectAccess用戶端也需要利用https來連接 __網路__  __位置__  __伺服器__  __ __ APP1，而我們將設定讓用戶端透過 __https://nls\.sayms\.local/__ 來連接 __網路位置伺服器__ APP1，因此伺服器APP1需要一個主體名稱為nls\.sayms\.local的 __伺服器驗證__ 憑證
* 我們將替這兩台伺服器向企業CA申請 __伺服器驗證__ 憑證
  * 需可以自行指定主體名稱
  * 可透過向企業CA申請 __Web__  __伺服器__ 憑證範本來擁有 __伺服器__  __驗證__ 憑證，但需修改此範本的設定

# 變更「Web伺服器」憑證範本設定

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_84.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_85.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_86.png)

# 透過群組原則來讓內部電腦自動安裝IPsec所需的憑證 - 1

* 若DirectAccess用戶端與伺服器雙方使用IPsec電腦憑證方式來驗證身分的話，則雙方都需要 __用戶端驗證__ 憑證
  * 透過群組原則來讓內部所有電腦都自動擁有 __用戶端驗證__ 憑證
  * 可以透過企業CA的 __工作站驗證__ 範本來發放 __用戶端驗證__ 憑證，不過需修改此範本的設定

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_87.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_88.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_89.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_90.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_91.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_92.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_93.png)

分別到DirectAccess伺服器與用戶端Win10PC1上來檢查是否已經自動安裝 __用戶端驗證__ 憑證

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_94.png)

透過Default Domain Policy：展開 __電腦設定__ 原則Windows設定安全性設定公開金鑰原則

# 資源伺服器APP1的設定

* APP1需要 __伺服器驗證__ 憑證
  * 用戶端需要透過https來連接扮演 __網路位置__  __伺服器__ 角色的APP1

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_95.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_96.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_97.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_98.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_99.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_100.png)

# 將憑證繫結到APP1內的IIS網站

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_101.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_102.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_103.png)

# IPv4資源伺服器APP2的設定

APP2為僅支援IPv4的Windows Server 2016，用戶端透過DirectAccess伺服器的DNS64/NAT64來存取APP2內的網頁與檔案資源。到此電腦安裝 __網頁__  __伺服器__  __\(IIS\)__ 角色、建立供測試用的共用資料夾

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_104.png)

http://app2\.sayms\.local/

\\\\APP2\\TestFolder2

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_105.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_106.png)

# 在DirectAccess伺服器DA1的設定

* 安裝「伺服器驗證」憑證
  * DirectAccess用戶端需要利用HTTPS來連接DirectAccess伺服器，故我們需要在DirectAccess伺服器安裝 __伺服器驗證__ 憑證
* 將此伺服器設定為DirectAccess伺服器
  * 此伺服器需要先安裝 __遠端存取__ 角色，不過在前幾個章節內已經安裝過了，故此處僅需將其設定為DirectAccess伺服器即可

# 安裝「伺服器驗證」憑證

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_107.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_108.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_109.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_110.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_111.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_112.png)

# 將伺服器設定為DirectAccess伺服器 - 1

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_113.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_114.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_115.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_116.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_117.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_118.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_119.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_120.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_121.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_122.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_123.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_124.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_125.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_126.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_127.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_128.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_129.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_130.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_131.png)

若未執行此指令的話，則在下一個步驟的圖中的 __Network adapters__ 處可能會顯示黃色的驚嘆號警告

---

若出現與Teredo有關的ICMP警告的話，可不必理會，因為我們已經將內部主機的Windows防火牆的ICMP流量開放了

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_132.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_133.png)

若 __Windows__  __防火牆__ 被停用，或 __網域設定檔__ 與 __公用設定檔__ 之一被停用的話 ，DirectAccess功能將無法正常運作

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_134.png)

DirectAccess原則\-DaServerToCorp：用在建立 __內部網路通道__

DirectAccess原則\-DaServerToInfra：用在建立 __基礎結構通道__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_135.png)

或執行netsh  interface  teredo  show state

---

DirectAccess用戶端設定用來套用到用戶端群組DirectAccessClients、DirectAccess伺服器設定用來套用到DirectAccess伺服器DA1

# 將用戶端Win10PC1搬移到內部網路來套用群組原則設定

* 待會將用戶端Win10PC1搬移到網際網路\( __測試網路__ \)後，其將獲得public IP，因此會採用6to4
* 若使用6to4無法連線成功，且無法順利自動改用Teredo或IP\-HTTPS的情況的話：
  * 請透過PowerShell指令netsh interface 6to4 set state disabled將6to4停用，以便直接改用Teredo或IP\-HTTPS。也可以利用netsh interface 6to4 show state指令來查看目前6to4的啟用狀態

  * 透過GPO「 __DirectAccess__  __用戶端設定__ 」
    * 電腦設定原則系統管理範本網路TCPIP設定值IPv6轉換技術雙擊 __設定__  __6to4__  __狀態__ 點選 __已啟用__ 在 __從下列狀態中選取__ 選項中選擇 __停用狀態__

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_136.png)

\* 若要將Teredo停用或啟用的話： __netsh__  __ interface __  __teredo__  __ set state __  __disabled__  __、__  __netsh__  __ interface __  __teredo__  __ set state client__

---

即使6to4的功能都正常，然而目前電信廠商一般並未開放6to4的流量（通訊協定號碼為41），因此DirectAccess用戶端可能還是無法透過6to4來連接DirectAccess伺服器

# 檢查用戶端Win10PC1的DirectAccess設定 - 1

* \.sayms\.local規則
  * 2002:8c73:803:3333::1扮演DNS64角色的DirectAccess伺服器
* DirectAccess\-NLS\.sayms\.local規則
  * 它是 __網路位置伺服器__ 的主機名稱，用戶端在網際網路上欲查詢DirectAccess\-NLS\.sayms\.local的IP位址時，會向一般DNS伺服器SERVER1來查詢，但並無法查詢到，因此用戶端無法與 __網路位置伺服器__ 溝通，便據以判斷它是位於網際網路
  * 此DNS伺服器是透過DHCP伺服器SERVER1的選項來指定的
  * 若用戶端在網際網路上欲查詢的主機名稱或其尾碼未列在表格中的話，則它也會向一般DNS伺服器\(SERVER1\)來查詢

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_137.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_138.png)

---

或使用Get-DnsClientNrptPolicy
可以透過【點擊步驟2（遠端存取伺服器）的編輯鈕點擊網路介面卡】來查看DNS64的IPv6位址
若要在DirectAccess伺服器上查看或修改以上3個規則的話：【點擊步驟3（基礎結構伺服器）的編輯鈕點擊DNS】

* Win10PC1目前是在內部網路，故以上NRPT規則無效
* 可以透過Get\-NCSIPolicyConfiguration指令來查看 __網路連線狀態__  __指示器__ \(NCSI\)的相關設定
  * 用戶端透過URL  __https__  __://nls\.sayms\.local/__ 來連接 __網路位置__  __伺服器__
  * DirectAccess伺服器需要憑證\(在前面已經申請過憑證了\)。若用戶端可以透過此URL來連接 __網路位置伺服器__ 的話，便表示用戶端是位於內部網路

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_139.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_140.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_141.png)

ConnectLocally表示用戶端在內部網路

用戶端的Network Connectivity Assistant服務需已啟動

---

NCSI：Network Connectivity Status Indicator
若用戶端的Network Connectivity Assistant服務未啟動的話，則執行Get-DAConnectionStatus指令會有錯誤警示
若發生用戶端Windows防火牆被停用的情況時，請手動啟用之，否則會影響到DirectAccess功能

# 將用戶端Win10PC1搬移到網際網路來測試DirectAccess - 1

Win10PC1搬移到網際網路\( __測試網路__ \)後，會從DHCP伺服器\(SERVER1\)取得IP位址與DNS伺服器等選項設定值，此時將滑鼠指標移到桌面畫面右下角的 __網路__ 圖示，它會顯示 __網際網路存取__ 訊息

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_142.png)

用戶端無法透過 __測試網路__ 中的DNS伺服器SERVER1解析 __網路位置伺服器__ nls\.sayms\.local的IP位址，因此無法連接到 __網路位置伺服器__ ，此時用戶端會認為其位於網際網路，故NRPT規則便有效

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_143.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_144.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_145.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_146.png)

IPv6位址2001:db8:1::2就是IPv6主機APP1的IPv6位址

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_147.png)

http://app1\.sayms\.local/

\\\\APP1\\TestFolder

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_148.png)

---

若出現Status：ActionableError、Substatus：FirewallDisabled訊息的話，請將用戶端的Windows防火牆開啟、並建議重新啟動用戶端電腦

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_149.png)

IPv6位址：由DirectAccess伺服器的DNS64自行利用 __動態首碼__  \+  __伺服器__  __APP2\(IPv4__  __主機__  __\)__  __的__  __IPv4__  __位址__ 所組合成

http://app2\.sayms\.local/

\\\\APP2\\TestFolder2

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_150.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_151.png)

可以利用 __Get\-__  __NetIPAddress__ 指令來查看用戶端IPv6位址資訊

用戶端是被設定為利用https://da1\.sayda\.com:443/IPHTTPS來連接DirectAccess伺服器

netsh interface teredo show state指令可得知用戶端的Teredo設定，由圖中可看出其Teredo伺服器被設定為da1\.sayda\.com、目前的狀態為qualified

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_152.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_153.png)

DirectAccess用戶端與伺服器之間所建立的IPsec SA

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_154.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_155.png)

到DirectAccess伺服器來查看DirectAccess用戶端的連線狀態

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_156.png)

---

DirectAccess用戶端與伺服器之間是利用IPsec來驗證電腦與使用者

# 將用戶端Win10PC1搬移到客戶網路來測試DirectAccess

用戶端位於 __客戶網路__ 時，其使用私人IP位址192\.168\.137\.0/24，故會先使用Teredo來連接DirectAccess伺服器，此時因為與Teredo有關的環境設定都已經完成了，故可以連線成功

將用戶端電腦Win10PC1搬移到 __客戶網路__ 後，Win10PC1會從扮演NAT\(ICS\)角色的NAT1獲得私人IP位址192\.168\.137\.0/24與DNS伺服器等選項設定值，此時將滑鼠指標移到桌面畫面右下角的 __網路__ 圖示，它會顯示 __網際網路存取__ 訊息

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_157.png)

# 用戶端位於客戶網路(NAT)之後的測試 - 1

用戶端無法透過 __客戶網路__ 中的DNS伺服器\(NAT1的 __DNS__  __轉接__  __代理__ \)解析到 __網路位置__  __伺服器__ nls\.sayms\.local的IP位址，因此用戶端會認為其“不是”位於內部網路，故NRPT規則便有效

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_158.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_159.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_160.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_161.png)

---

若出現Status：ActionableError、Substatus：FirewallDisabled訊息的話，請將用戶端的Windows防火牆開啟、並建議重新啟動用戶端電腦
ConnectRemotely：可能需等一小段時間

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_162.png)

IPv6位址2001:db8:1::2就是IPv6主機APP1的IPv6位址

http://app1\.sayms\.local/

\\\\APP1\\TestFolder

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_163.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_164.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_165.png)

IPv6位址：由DirectAccess伺服器的DNS64自行利用 __動態首碼__  \+  __伺服器__  __APP2\(IPv4__  __主機__  __\)__  __的__  __IPv4__  __位址__ 所組合成

http://app2\.sayms\.local/

\\\\APP2\\TestFolder2

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_166.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_167.png)

---

還可利用Get-NetIPAddress指令來查看用戶端的IPv6位址資訊

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_168.png)

netsh interface teredo show state指令可得知用戶端的Teredo設定，由圖中可看出其Teredo伺服器被設定為da1\.sayda\.com、目前的狀態為qualified

透過ipconfig /all指令來查看用戶端的Teredo設定，圖中用戶端的IPv6位址為2001:0:8c73:803:50:bfa:738c:f7cc

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_169.png)

DirectAccess用戶端與伺服器之間所建立的IPsec SA

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_170.png)

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_171.png)

到DirectAccess伺服器來查看DirectAccess用戶端的連線狀態

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_172.png)

若是直接將用戶端從 __測試網路__ 搬移到 __客戶網路__ 的話，則可能圖中還是顯示之前的IP\-HTTPS通訊協定，而不是Teredo，此時建議先將用戶端搬移回 __內部網路__ 、待圖中用戶端的連線資訊自動消失後，再將用戶端搬移到 __客戶網路__ ，這時候就會看到正確的Teredo資訊

---

DirectAccess用戶端與伺服器之間是利用IPsec來驗證電腦與使用者
本演練環境還需要供第12章的NAP演練來使用，故暫時不要破壞它，建議利用虛擬軟體的快照功能（snapshot）將這些虛擬機器狀態儲存起來

# 啟用對Windows 7用戶端的支援 - 1

在DirectAccess伺服器開啟 __遠端存取管理__ 主控台點擊左方的 __設定__ 點擊 __步驟__  __2__ （遠端存取伺服器）處的 __編輯__  __…__ 鈕

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_173.png)

需Windows 7 Enterprise或Windows 7 Ultimate

* Windows 7的Windows PowerShell不支援某些查詢狀態的指令
  * 例如Get\-DAConnectionStatus、Get\-NetIPAddress、
  * Get\-NetIPHTTPSConfiguration

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_174.png)

  * 若要查看用戶端是位於內部網路或外部網路\(網際網路\)的話，可改用netsh  dnsclient  show  state指令

![](WS2016%E7%B6%B2%E8%B7%AF%E8%88%87%E7%B6%B2%E7%AB%99%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA240-Ch11-%E9%80%8F%E9%81%8EDIRECTACCESS%20%E7%9B%B4%E6%8E%A5%E5%AD%98%E5%8F%96%E5%85%A7%E9%83%A8%E7%B6%B2%E8%B7%AF%E8%B3%87%E6%BA%90_175.png)

  * 若要查詢用戶端的IP\-HTTPS設定的話

