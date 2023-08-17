## **指標範例**

![](images/lemans.png " ")

STAR-CCM+ 的效能通常使用 LeMans 基準搭配 17 和 105 百萬個儲存格來測量。下一個圖表顯示使用更多節點對程式實際執行有何影響，而調整規模確實接近 100%。此 Runbook 尚未討論的 RDMA 網路，只有在儲存格 / 核心比率開始下降時，才會開始區分與一般 TCP 執行。

**1700 萬個細胞**

![](images/RunTime_17M.png " ")

**1050 萬個細胞**

![](images/Scaling_105M.png " ")

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月