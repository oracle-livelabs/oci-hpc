# 簡介

曾經在停車場發現你有傘子，晚上沒有雨或下雨，沒有雪刷的狀況下自己，因為你認為這些資訊是虛假的嗎？您是否厭倦了當地風人預測天氣不當，而且想要做些事情？其實，這是您可能會在其中找到值的實驗室。現在，我們將討論並引導您完成在 OCI 基礎架構上設定和執行 WRF 的過程。我還會提供您想要執行 WRF 而不需要瞭解如何設定的程序檔。

預估實驗室時間：90 分鐘

### 關於 WRF

WRF 是氣象研究和預測模型的縮寫。它是針對大氣研究與營運預測應用程式設計的中度數值氣象預測系統。美國國家大氣研究中心 (NCAR)、國家海洋與大氣總署 (National Centers for Environment Prediction (NCEP) 與地球系統研究實驗室 (Earth System Research Laboratory)、美國空軍、海軍研究實驗室、俄克拉荷馬大學與聯邦航空管理局 (Federal Aviation Administration，FAA) 90 晚期開始的 WRF 合作夥伴關係。WRF 是開放原始碼，可在 github 上使用。WRF 可讓研究人員使用透過觀察和分析收集的真實資料，或理想的大氣條件來模擬天氣。WRF 提供操作預測平台，許多貢獻者都協助進行物理、數字和資料共通的進步。WRF 目前正於 NCEP 與國際其他預測中心營運使用，這是相當大的交易。

### 目標

此實驗室涵蓋的主題：

*   下載相依性和 WRF
*   為 WRF/WPS 編譯程式庫
*   編譯 WRF 與 WPS
*   使用 Geogrid 建立 WRF 網域
*   下載 GFS 資料並執行 Ungrib 和 Metgrid
*   以真實資料執行 WRF

### 必要條件

此實驗室假設您具有：

*   Oracle Free Tier 或 Paid Cloud 帳戶

## 確認

*   **作者** - Big Compute 解決方案工程師 Brian Bennett
*   **上次更新者 / 日期** - Big Compute 的 Brian Bennett，2020 年 12 月