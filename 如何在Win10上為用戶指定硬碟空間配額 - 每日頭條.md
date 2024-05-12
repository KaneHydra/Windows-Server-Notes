若一台Windows 10 PC供多個用戶使用，那這台PC很可能面臨硬碟空間被Windows 10中的某個用戶使用殆盡的困擾。

好在在Windows 10中，微軟為計算機的管理員提供了一個便利的功能，即「配額設置」。

通過配額設置，計算機的管理員可以為Windows 10 PC上的每個用戶分指定硬碟配額，以防計算機的硬碟空間被某個用戶的文件或者數據填滿。

要了解如何為Windows 10 PC上的用戶指定硬碟配額，請參閱本期IT之家極客學院給出的詳細步驟。

![](https://i1.kknews.cc/HmSLeb81_Q1OIFk4nA9XsV3w643aNDOf_ZSDZH8/0.jpg)

**基於所有新用戶的磁碟配額分配【以本地磁碟(D:)為例】**

要基於所有新用戶來為Windows 10 PC中的用戶指定硬碟配額，請依照以下的步驟操作：

打開文件資源管理器（這台電腦）；

![](https://i1.kknews.cc/cIqkJOqFLIlHYw25YON3IuzJMHd-AWe-PrWjy7s/0.jpg)

將滑鼠光標移至你要操作的卷的圖標之上，單擊滑鼠右鍵，在上下文菜單中點擊「屬性」；

![](https://i1.kknews.cc/M2KrYyWOLgsLJD6QoI-81Z4o6iimjb84BEYQhDM/0.jpg)

在該卷的屬性窗口的頂部，你將看到若干選項卡，點擊「配額」選項卡；

在「配額」選項卡下，你將看到一個按鈕，該按鈕上的文字為「顯示配額設置」；

![](https://i1.kknews.cc/QtoZC-2BYPwyZes9IondI5Q_5Mo9aFbP5WoBr7Y/0.jpg)

以滑鼠左鍵單擊「顯示配額設置」按鈕，Windows 10將彈出「本地磁碟(D:)的配額設置」窗口；

![](https://i1.kknews.cc/K0mrHE5PH5PkobxY9UEA8yTvccfYH4NjPx5risY/0.jpg)

在「本地磁碟(D:)的配額設置」窗口下，你將看到磁碟配額的狀態以及相關的設置項，要為Windows 10 PC上的用戶指定硬碟配額，則請勾選配額設置窗口下「啟用配額管理」前的複選框；

當「啟用配額管理」的複選框被勾選後，窗口中不可用的設置項將變為可用，此時，請勾選「拒絕將磁碟空間分配給超過配額限制的用戶」；

在「為該卷上的新用戶選擇默認配額限制：」下，選擇「將磁碟空間限制為」，此時，該設置項後的文本輸入框和下拉選擇框將處於可用的狀態；

在下拉選擇框中選擇你要為Windows 10 PC上的用戶分配的磁碟空間大小的合適的單位，再在文本輸入框中輸入要分配給Windows 10 PC上的用戶的磁碟空間大小的數值；

將目光移至「將警告等級設為」設置項，該設置項後有一個文本輸入框和一個下拉選擇框，在下拉選擇框中選擇你要設置的可觸發警告的磁碟空間使用量的單位，再在文本輸入框中輸入可觸發警告的磁碟空間使用量的數值；

在「選擇該卷的配額記錄選項：」設置項下勾選「用戶超出配額限制時記錄事件」和「用戶超過警告等級時記錄事件」前的複選框（可選設置）。

設置完成後，設置項狀態如下：

![](https://i1.kknews.cc/Tqjg8RDT58Uvr1aidyOQTL218B0g_vVhCCqdD-k/0.jpg)

點擊「確定」按鈕，Windows 10將彈出對話框如下：

![](https://i2.kknews.cc/AZ65fdXSbMRwJM38W19v5sit2ZjbF-GkkYr3tbU/0.jpg)

點擊「確定」按鈕完成設置；

重啟計算機。

此時，基於所有新用戶的磁碟配額分配即已完成。

**基於特定用戶的磁碟配額分配【以本地磁碟(D:)為例】**

要基於特定用戶來為Windows 10 PC中的用戶指定硬碟配額，請依照以下的步驟操作：

在「為該卷上的新用戶選擇默認配額限制：」下，選擇「不限制磁碟使用」或「將磁碟空間限制為：」（可根據實際情況選擇限制方案，有關「將磁碟空間限制為：」的設置方法請參閱「基於所有新用戶的磁碟配額分配【以本地磁碟(D:)為例】」小節中的相關內容）；

設置完成後，設置項狀態如下：

![](https://i2.kknews.cc/LkctTJbHI_U5ozHrRVwmGeGHVS4ayfO9Z7vzPcM/0.jpg)

點擊配額設置窗口中的「配額項…」按鈕，Windows 10將彈出「本地磁碟(D:)的配額項」設置窗口如下：

![](https://i1.kknews.cc/V9-NnyzDgecomUo8lVuBYr4zIv9BGuJlYa9q3V8/0.jpg)

在菜單欄中依次點擊「配額」-「新建配額項」，Windows 10將彈出「選擇用戶」窗口如下：

![](https://i2.kknews.cc/1bZbFVBz9_uQ9XleiqsHqWOsgx2paqR8lu1JId0/0.jpg)

在「輸入對象名稱來選擇」下的文本輸入框內輸入要設置的目標用戶的名稱，點擊「檢查名稱」按鈕；

若用戶名稱有效，輸入框中的文字將變為如下圖所示的格式：

![](https://i1.kknews.cc/BQH0oX11iD8b3e6oonTuunUP-tdCYZtw7CukScQ/0.jpg)

點擊「確定「按鈕，Windows 10將彈出「添加新配額項」設置窗口如下：

![](https://i1.kknews.cc/9Dv14A-HR_06XQY9VpJ4PFc9Cf7G68o-Z3sugQY/0.jpg)

在「設置所選用戶的配額限制：」下，選擇「將磁碟空間限制為」，此時，該設置項後的文本輸入框和下拉選擇框將處於可用的狀態；

在下來選擇框中選擇你要為目標用戶分配的磁碟空間大小的合適的單位，再在文本輸入框中輸入要分配給目標用戶的磁碟空間大小的數值；

![](https://i1.kknews.cc/k7Gb0ijVXLIWWEBIIy7rtltvBoz4_RTZQxMN3H4/0.jpg)

點擊確定按鈕；

此時，在「本地磁碟(D:)的配額項」設置窗口的列表中，你將看到一個新項已被添加，如下圖所示：

![](https://i1.kknews.cc/UHtWMoYjj33hZRcwXDvzxPkxR95z3LUmRprfX6g/0.jpg)

若要為更多Windows 10 PC用戶指定硬碟配額，請參閱並重複本小節中的特定步驟；

關閉「本地磁碟(D:)的配額項」設置窗口；

設置完成後，設置狀態如下：

![](https://i2.kknews.cc/HZs0LITokjxAY2eKurtd7PQFNzzST-B88uJDIAE/0.jpg)

點擊「本地磁碟(D:)的配額設置「窗口下的」確定」按鈕，Windows 10將彈出對話框如下：

![](https://i2.kknews.cc/tbBg-NaJZT2il6T-SAMOzzqeETOSBNmmkFddvp0/0.jpg)

此時，基於卷的磁碟配額分配即已完成。

**取消面向所有用戶的磁碟空間配額限制【以本地磁碟(D:)為例】**

若要取消面向所有用戶的磁碟空間的配額限制，請參閱「基於所有新用戶的磁碟配額分配【以本地磁碟(D:)為例】」小節或「基於特定用戶的磁碟配額分配【以本地磁碟(D:)為例】」小節中的相關步驟，調出「本地磁碟(D:)的配額設置」窗口；

將「啟用配額管理」前的複選框取消勾選；

![](https://i2.kknews.cc/2cVN-5VK9zg5-KxBI_Wix0j2MBowu3c8h1I6g2c/0.jpg)

點擊「本地磁碟(D:)的配額設置」窗口下的「確定」按鈕，Windows 10將彈出對話框如下：

![](https://i1.kknews.cc/ufxhQgBa4ibTOi2OO3OI6a00v-hmh9GiiSELNLo/0.jpg)

此時，面向所有用戶的磁碟空間配額限制即已取消。

**取消面向特定用戶的磁碟空間配額限制【以本地磁碟(D:)為例】**

若要取消面向特定用戶的磁碟空間的配額限制，請參閱「基於特定用戶的磁碟配額分配【以本地磁碟(D:)為例】」小節中的相關步驟，調出「本地磁碟(D:）的配額項」設置窗口；

![](https://i1.kknews.cc/1NZQUhwhtvPym0damctNlVemTi4TmWJSz2hFdKc/0.jpg)

在此窗口裡的列表中雙擊你要設置磁碟空間配額限制的用戶名稱，Windows 10將彈出設置窗口如下：

![](https://i1.kknews.cc/XdNg-y2vNj85_bj_gpMWo28JeJaOGdM9ddnFlCM/0.jpg)

在此窗口中選擇「不限制磁碟使用」項；

![](https://i2.kknews.cc/HRPoDxdcOhg9co_Cjg94UxFkaNxJs8Nx0LNOAFQ/0.jpg)

點擊設置窗口下的「確定」按鈕；

關閉「本地磁碟(D:）的配額項」設置窗口；

點擊「本地磁碟(D:）的配額設置」窗口；

重啟計算機。

好的，以上就是本期IT之家極客學院的全部內容，開開心心地玩兒去吧~
