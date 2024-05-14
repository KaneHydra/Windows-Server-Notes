第 13 章 自動信任根CA

13\-1 自動信任CA的設定準則

13\-2 自動信任內部的獨立CA

13\-3 自動信任外部的CA

# 自動信任CA的設定準則

* 可以透過AD DS群組原則，來讓網域內所有電腦都自動信任指定的根CA，也就是自動將這些根CA的憑證發送、安裝到網域內所有電腦
  * 若是企業根CA
    * AD DS會自動透過群組原則將企業根CA的憑證發送到網域內所有電腦，也就是說網域內所有電腦都會自動信任企業根CA
  * 若是安裝在成員伺服器上的獨立根CA，且是由具備存取AD DS權利的網域系統管理員所安裝的
    * AD DS會自動透過群組原則將此獨立根CA的憑證發送到網域內所有電腦
  * 若是安裝在獨立伺服器的獨立根CA、或是安裝在成員伺服器上的獨立根CA但執行安裝工作的使用者不具備存取AD DS的權利
    * 需要另外透過 __受信任的根憑證授權單位原則__ ，來將此獨立根CA的憑證自動發送到網域內所有電腦
  * 若不是架設在公司內部的獨立根CA，而是外界的獨立根CA
    * 需要另外透過 __企業信任原則__ ，來將此獨立根CA的憑證自動發送到網域內所有電腦

---

Windows電腦只要信任了根CA，它們預設就會自動信任根CA之下所有的次級CA

# 自動信任內部的獨立CA

* 利用以下兩大步驟來練習將名稱為 __Server1 Standalone Root CA__ 的獨立根CA的憑證，自動發送到網域內的所有電腦
  * 下載獨立根CA的憑證並存檔
  * 將獨立根CA的憑證匯入到 __受信任的根憑證授權單位原則__

# 下載獨立根CA的憑證並存檔

下載CA憑證：會將其檔名設定為certnew\.cer \(內含憑證\)

下載CA憑證鏈結：會將其檔名設定為certnew\.p7b的檔案\(內含憑證與憑證路徑\)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_0.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_1.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_2.png)

若您的電腦的 __根憑證存放區__ 內已經有該CA的憑證，也就是此電腦已經信任該CA的話

# 將CA憑證匯入到「受信任的根憑證授權單位原則」- 1

假設要透過Default Domain Policy GPO來設定

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_3.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_4.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_5.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_6.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_7.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_8.png)

網域內所有電腦在套用這個原則後，它們就都會自動信任此獨立根CA

# 自動信任外部的CA

* 建立 __憑證信任清單__ \(certificate trust list，CTL\)，然後透過 __企業信任原則__ 來將 __憑證信任清單__ 內所有根CA的憑證發送到網域內所有電腦
  * 可透過 __憑證信任清單__ 來信任CA所發放的憑證只能夠用在單一用途上，例如伺服器驗證，其他用途一概拒絕信任
* 演練步驟
  * 下載獨立根CA的憑證並存檔
    * 利用ADCS所架設的CA
    * 利用其他軟體所架設的CA
  * 申請可以將 __憑證信任清單__ 簽章的憑證
  * 建立 __憑證信任清單__ \(CTL\)

# 下載獨立根CA的憑證並存檔

* 下載名稱為External Standalone Root CA的獨立根CA的憑證並存檔，假設其檔案名稱為ExtCertnew\.p7b
  * 若這台獨立根CA是利用Windows Server的 __Active Directory__  __憑證服務__ 所架設的，則其操作方法與前一節相同
  * 若這台根CA是利用其他軟體所架設，則請參考該軟體的文件來操作

# 申請可以將「憑證信任清單」簽章的憑證

* __憑證信任清單__ 需經過簽章
  * 假設要向Sayms Enterprise Root CA企業根CA申請用來簽章的憑證
  * 請到網域控制站上登入，執行certmgr\.msc

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_9.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_10.png)

---

從Windows Vista、Windows Server 2008開始有此現象

# 建立「憑證信任清單(CTL) 」- 1

以下所要建立的 __憑證信任清單__ \(CTL\) 內包含名稱為 __External Standalone Root CA__ 的外部獨立根CA的憑證，也就是要讓網域內所有電腦都自動信任此獨立根CA，而我們將透過Default Domain Policy GPO來設定

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_11.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_12.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_13.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_14.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_15.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_16.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_17.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_18.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_19.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_20.png)

![](WS2019AD%E5%BB%BA%E7%BD%AE%E5%AF%A6%E5%8B%99-CA259-Ch13-%E8%87%AA%E5%8B%95%E4%BF%A1%E4%BB%BB%E6%A0%B9CA_21.png)

透過 __憑證信任清單__ 所信任的CA憑證，並不會顯示在使用者電腦的 __受信任的根憑證授權單位__ 存放區

將CTL匯出存檔：對著此CTL按右鍵所有工作匯出

匯入CTL：對著 __企業信任__ 按右鍵匯入

