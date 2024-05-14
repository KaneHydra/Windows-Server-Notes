---
id: RDP遠端桌面
aliases: []
tags: []
---

## 遠端桌面連線

用沒有加入網域的Server3做測試  
工具 => 電腦管理 => 本機使用者和群組  
先新增要允許遠端的使用者

系統內容 => 遠端 => 允許連線到此電腦 => 選取使用者

啟動遠端桌面服務後,  
也可以直接新增使用者到 _Remote Desktop Users_ 的群組  
也可以用 powershell 啟動遠端桌面服務

```sh
# Enable Remote Desktop
(Get-WmiObject Win32_TerminalServiceSetting -Namespace root\cimv2\TerminalServices).SetAllowTsConnections(1,1) | Out-Null
(Get-WmiObject -Class "Win32_TSGeneralSetting" -Namespace root\cimv2\TerminalServices -Filter "TerminalName='RDP-tcp'").SetUserAuthenticationRequired(0) | Out-Null
Get-NetFirewallRule -DisplayName "Remote Desktop\*" | Set-NetFirewallRule -enabled true
```

Server3沒有加入網域,看本機安全性原則  
安全性原則 => 本機原則 => 使用者權限指派  
允許透過遠端桌面服務  
預設會看到兩個群組  
Administrators跟Remote Desktop Users

但是要在DC開啟遠端桌面服務  
可以檢查一下  
群組管理原則 => Default Domain Controllers Policy => 電腦設定 => 原則 => Windows 設定 => 安全性設定 => 本機原則 => 使用者原則  
允許透過遠端桌面服務登入  
這裡是尚未定義  
由於衝突的關係,他自動套用了本機安全性原則的遠端桌面服務(只有Administrators群組)

所以這裡我們把Administrators跟RemoteDesktopUsers都加進來  
就可以正常連線了

如果要在DC上面找 Remote Desktop Users 群組  
在 abc.com => Built-in 下面
