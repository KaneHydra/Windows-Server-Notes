---
id: DomainController
aliases: []
tags: []
---

# Domain Controller

domain 介紹:

> 教學資料 => 筆記 => AD => Domain介紹.pptx

## 獨立伺服器 Stand Alone Server vs 網域控制站 Domain Controller

> [!note] 查看目前工作群組:
> 設定 => 系統 => 系統資訊 => 網域工作群組

-   獨立伺服器 SA (Stand Alone Server) 建立的使用者是本機使用者
-   網域控制站 DC (Domain Controller Server) 搭配 AD 做資源整合,讓使用者在網域內的地方登入
-   目錄伺服器 AD (Active Directory Server) 網域內資源整合查詢:
    -   印表機、網路磁碟機、共享資料夾
    -   Domain 網域: 伺服器組成, 三角形圖示
    -   Tree 樹: 網域組成, 多個三角形圖示
    -   Forest 森林: 多個樹圖示
-   同個Tree的網域之間,系統會自動建立:
    -   雙向父子信任關係, 雙向轉移性
    -   權限足夠就可以存取資源
-   Forest 之間的 Tree 做樹系信任就能做資源整合

-   單一 Domain:
    -   可以建立多個 OU (Orginazition Unit)
    -   加強網路安全性
    -   群組原則 (GPO)
    -   單一網域的安全性原則不能跨網域
        -   除非設定群組原則?
-   多個 Domain:
    -   需要多個 domain controller

## 新增 Domain Controller 網域控制站DC

> 每個網域裏面至少要有一臺 DC (樹根)

### 安裝DC:

伺服器管理員 => 儀表板 => 新增角色及功能  
在您開始前: 下一步  
安裝類型: 角色或功能型安裝 => 下一步  
伺服器選取項目: 從服務器集區選取伺服器 => 選你要的 => 下一步  
伺服器角色: Active Directory 網域服務 => 新增功能 => 下一步  
功能: 不要動 => 下一步  
AD DS: 下一步  
確認: 安裝

### 設定DC

> 右上角 管理的左邊有個黃色三角形驚歎號
> 部屬後設定 => 將此伺服器升級爲網域控制站

1. 選取部屬作業:
    - 網域類型要看情況選擇
    - 如果是第一台: 新增樹系 Add a new Forest
    - 如果是備援伺服器: 將網域控制站新增到現有網域
    - 如果是其他: 新增網域到現有樹系 => 子系網域
    - 根網域名稱: abc.com
2. Domain Controller Option
    - 樹系功能等級: 考慮樹系內其他電腦的作業系統版本484比較舊,取版本低的
    - 網域功能等級: 考慮網域內(其他同上)
    - 設定Domain Services Restore Mode (DSRM) 目錄服務還原模式 的密碼
3. DNS 選項:
    - 不用指定DNS委派
4. Additional Option 其他選項:
    - NetBIOS 網域名稱, 如果發現後面被自動加了數字, 表示有重複
    - 他會自己跳
5. 路徑 path, 用預設值
6. 檢閱選項: 讓你檢查的, 可以輸出 powershell script
7. 先決條件檢查: 檢查完成沒問題就安裝

### 設定DNS Server

DNS Server 的 IP 本來是 127.0.0.1  
改成剛剛設定成 DNS Server 那臺電腦的 IP: 192.168.1.1  
網域內的全部電腦都要改

### 查看 AD 使用者, 群組, 電腦

設定完之後可以在右上角 工具 => AD 使用者和電腦 查看  
Enterprise Admins 是系統最高管理群組  
電腦管理裏面的`使用者管理`就消失了, 變成要在 `AD 使用者和電腦` 或 `AD 群組` 查看

之前新增的非 admin-users 會變成無法登入  
本機安全性原則 => 本機原則 => 使用者權限指派  
這裏本來的 Users 就不見了  
或是在`群組原則管理`查看  
群組原則管理編輯器 => 電腦設定 => 原則 => Windows設定 => 安全性設定 => 本機原則 =>  
使用者要加入網域,才能登入,而且要從其他電腦登入,不能直接在伺服器這臺電腦登入  
電腦名稱/網域邊更 => 成員隸屬 => 網域  
這裏必須要網域系統管理員帳號密碼才能設定

### 新增AD使用者

伺服器管理員 => 工具 => Active Directory 使用者和電腦  
左邊選網域名稱 => 最下面 Users => 右邊框框 => 右鍵 => 新增 => 使用者  
記得設定密碼, 把使用者登入時必須修改密碼的勾取消, 密碼永久有效打勾

### 邊更成員隸屬, 成爲網域成員

本機 => 右鍵內容 => 系統 => 變更設定 => 電腦名稱 => 邊更 => 成員隸屬 => 網域  
網域輸入 abc.com  
這裏需要網域系統管理員的帳號密碼才能設定  
abc\administrator  
設定好之後, 登入時改爲使用 abc\使用者名稱

### 設定登入時段

`AD 使用者和電腦` => abc.com => Users => 選擇要設定的使用者 => 帳戶 => 登入時段

### Member Server 設定

如果另一臺 Server 不想升級爲 Domain Controller  
但是又想存取 Domain 內的資源, 就要加入 Member Server  
一樣就是邊更成員隸屬, 成爲網域成員  
從Member Server 登入伺服器管理員, 就沒有 AD 管理工具

> 如果想要在 client 端同時登入 local administrator 跟 domain administratotr  
> 可以在 client 端登入 domain administrator 之後  
> 開啓 使用者管理, 找 administrator, 設定密碼後, 把永久停用取消打勾

> 如果想要新增使用者  
> 伺服器管理員 => 右上角 工具 => 電腦管理  
> 左邊系統工具 => 本機使用者和群組 => 使用者 => 右邊視窗 右鍵 => 新使用者

### 在 Member Server 登入 domain administrator後 安裝 IIS server

新增角色及功能  
伺服器角色: 網頁伺服器(IIS)  
角色服務: 額外打勾 IP及網域限制, Windows驗證, FTP伺服器
