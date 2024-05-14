---
id: cmd-run-as-以不同使用者身分執行
aliases: []
tags: []
---

# runas - command

windows `runas` command is just like linux `su` command

[參考來源: is-there-an-equivalent-of-su-for-windows](https://serverfault.com/questions/16886/is-there-an-equivalent-of-su-for-windows)

```powershell
# in Command Prompt 1
# local account:
runas /profile /user:computernamehere\username cmd
runas /profile /user:localhost\username cmd
runas /profile /user:computernamehere\username pwsh
runas /profile /user:localhost\username pwsh
# domain account:
runas /profile /user:domainname\username cmd
runas /profile /user:username@domainname cmd
# 這時會跳一個視窗教你輸入密碼,輸入後會出現一個 Command Prompt 2

# 如果要開桌面/檔案管理員...
# 在 Command Prompt 1:
taskkill /f /IM explorer.exe
# 在 Command Prompt 2:
explorer.exe
# 就會開啓你登入的使用者的桌面

# Command Prompt 1 不要關

# 如果要開回去...
# 在 Command Prompt 2:
taskkill /f /IM explorer.exe
# 在 Command Prompt 1:
explorer.exe
```
