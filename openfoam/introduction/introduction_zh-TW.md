# 在 OCI 上執行 HPC 叢集和標準 VM 的 OpenFOAM 專案

## 簡介

本 Runbook 的設計目的是協助評估 Oracle Cloud Infrastructure 中的 OpenFOAM CFD 軟體。它會自動下載和設定 OpenFOAM。OpenFOAM 是自 2004 年起發行並主要由 OpenCFD Ltd 開發的免費開源 CFD 軟體。它在商業和學術機構大部分的工程和科學領域都有很大的用戶群。OpenFOAM 提供多種功能，可解決任何涉及化學反應、亂流和熱傳遞、聲學、固體力學和電磁學的複雜流體流動。

### 關於高效能運算 (HPC)

HPC 或超級運算等同於日常運算，只具備更強大的功能。它是高速處理大量資料的方法，使用多個電腦和儲存裝置作為緊密整合的結構。HPC 讓您探索並找出世界上最科學、工程和業務問題的答案。

叢集網路是透過高頻寬、超低延遲網路連線的高效能運算 (HPC) 執行處理集區。叢集中的每個節點都是鄰近其他節點的裸機機器。節點之間的遠端直接記憶體存取 (RDMA) 網路可提供低延遲的單一數位微秒，與企業內部部署 HPC 叢集相比。

在 OCI 區域內的 Oracle Cloud Infrastructure 上提供高效能運算。

OCI 中的 Oracle Marketplace Image 和 BM.HPC2.36 提供高效能運算執行處理。

Oracle Marketplace Image 中的高效能運算機架包括 HPC 叢集節點、叢集網路和 NFS 共用。

運算節點是透過叢集網路連線的，提供以 RDMA 為基礎的運算節點儲存存取。

目前，每一運算節點支援單一 BM。它可讓客戶在保護硬體和網路的同時進行 root 存取，而運算節點則是使用 BM.HPC2.36 虛擬化。

_預估時間_：2 小時

### 目標

*   使用透過遠端直接記憶體存取 (RDMA) 連線的 HPC 執行處理執行 OpenFOAM 專案
    
*   在 Oracle Cloud Infrastructure (OCI) 上使用一般標準虛擬機器 (VM) 執行 OpenFOAM 專案
    

### 必要條件

*   具有建立區間和堆疊權限的 Oracle Cloud Infrastructure 帳戶。
    
*   熟悉 OCI 主控台和 Oracle Cloud 資源 (亦即虛擬雲端網路、運算及儲存體) 非常有用。
    
*   假設您已完成 OCI Cloud Architect Associate 考試。
    

## 關於此研討會

*   使用 HPC 叢集執行 OpenFOAM 專案
    
*   使用 OCI 上的標準 VM 執行 OpenFOAM 專案
    

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者 / 日期** - Harrison Dvoor，2021 年 1 月