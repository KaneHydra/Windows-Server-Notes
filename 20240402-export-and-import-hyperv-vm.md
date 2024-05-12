# Hyper-V 匯入匯出虛擬機器 export and import hyper-v vm

參考檔案:`Windows Server 2012 R2 Hyper-V 匯出_匯入虛擬機器.docx`
去 `D:\LAB1130306` 下載老師的硬碟

匯入類型有3種:

-   就地登錄虛擬機械 (使用現有的唯一識別碼)
-   還原虛擬機器 (使用現有的唯一識別碼)
-   複製虛擬機器 (差異式硬碟不能用這個,會出問題), 耗時較久

所以現在要匯出 win10pc1 跟 win10pc2
之後再匯入 win10pc1 跟 win10pc2, 然後改名, 後面加上 `(匯入)`

在您開始前
尋找資料夾
`C:\Lab\win10pc1\Virtual Machine`

## 用 powershell 指令:

[clone-import-export-virtual-machines-hyper-v](https://woshub.com/clone-import-export-virtual-machines-hyper-v/)

```powershell
Export-VM -Name win10 -Path 'C:\VHD\export'


```
