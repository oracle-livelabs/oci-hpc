# 簡介

實作影像分類訓練是電腦視覺的「Hello World」。雖然有許多關於此主題的部落格文章，但大多數文章都會在不充分說明程式庫 API、基礎主參與者或兩者的情況下呈現解決方案。PyTorch 的 [Training a Classifier](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html) 指南是開始的一個很棒的地方，但一些程式碼的細節對新領域有太過稀疏的讀者來說。這場研討會是以該教學課程為基礎，並具有程式碼的逐行說明，有助於更清晰的學習途徑。本指南將告訴您如何使用 CPU 訓練模型，然後說明如何更新程式碼以使用 GPU 資源。請注意，免費層不提供 GPU 執行處理。

預估實驗室時間：60 分鐘

### 關於張力

為了更好或更糟糕，視覺表現法可協助情境化電腦科學術語、資料結構和演算法。對於張量，[KDnuggets 提供清楚說明](https://www.kdnuggets.com/2018/05/wtf-tensor.html)。當您在線上搜尋定義時，可能會在張量與空間用量的技術定義之間發生混淆。在技術上，向量是一維張量，而矩陣是二維張量，但通常張量是指大於向量和矩陣的 n 維容器。

![不同尺寸張數的視覺表示法](images/tensor.png)

這個詞彙相當重要，因為張量是建立人工智慧演算法的核心，並且深入討論如何最佳化運算資源。訓練模型是 GPU 使用率時的一般關注領域，因為 NVIDIA [Turing Tensor Cores](https://www.nvidia.com/en-us/data-center/tensor-cores/) 能夠大幅加速運算，但是進行一些調整。幸運的是，PyTorch 包含負責將張數傳輸至 GPU 的資料載入器。

### 目標

此實驗室涵蓋的主題：

*   張量
*   DataLoaders
*   革命性神經網路 (CNN)
*   遺失函數與最佳化處理程式
*   模型訓練
*   GPU 加速；

### 必要條件

此實驗室假設您具有：

*   Oracle Free Tier 或 Paid Cloud 帳戶

## 確認

*   **作者** - 大運算資深解決方案架構師 Justin Blau
*   **上次更新者 / 日期** - Justin Blau，Big Compute，2020 年 10 月