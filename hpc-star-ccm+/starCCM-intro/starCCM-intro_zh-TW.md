# Siemens STAR-CCM+ 介紹

## 簡介

Simcenter STAR-CCM+ 是模擬產品與設計的完整多物理解決方案。本 Runbook 可帶領您瞭解如何在 Oracle Cloud 上部署 Simcenter STAR-CCM+ 叢集，並在運算節點之間提供低延遲的網路連接。在 Oracle Cloud 上執行 Simcenter STAR-CCM+ 很簡單，請遵循本指南以取得所有秘訣和技巧。

請注意：您需要為 Simcenter STAR-CCM+ 提供自己的授權！

![](images/simulation.png " ")

## 架構

此 Runbook 的架構如下所示，我們將連接一個小型機器 (堡壘主機)。運算節點將會位於與 RDMA RoCE v2 網路連結的個別專用網路上。堡壘主機可從具有金鑰的任何人透過 SSH 存取 (如果您決定啟用該金鑰，則可透過 VNC) . 運算節點只能透過網路內的堡壘主機存取。只要 1 個虛擬雲端網路搭配 2 個子網路，一個公用網路和一個專用網路，即可實現這個目標。

### **基準基礎架構**

下列區域支援叢集網路。在每一種狀況中，我們建議您使用基準參照架構，然後視需要調整基準參照架構，以符合您的特定需求：

*   項目符號 VCN
*   公用子網路，安全清單，路由表
*   專用子網路，安全清單，路由表
*   網際網路閘道
*   NAT 閘道
*   計算節點
*   公用子網路中的防禦主機
*   專用子網路中的 HPC 運算節點

![](images/architecture.png " ")

以上基準基礎架構提供下列規格：

*   網路
    *   1 x 100 Gbps RDMA over converged ethernet (ROCE) v2
    *   延遲低至 1.5 微秒
*   HPC 運算節點 (BM.HPC2.36)
    *   6.4 TB 本機 NVME SSD 儲存體 (每個節點)
    *   每個節點 36 個核心
    *   每個節點 384 GB 記憶體

### **選擇性基礎架構**

### 儲存

在具有 HPC 資源配置的 NVME SSD 儲存體之上，您還可以為每個磁碟區連附 32k IOPS 的區塊磁碟區，以 Oracle 的最高效能 SLA 為後盾。如果您是使用我們的解決方案來啟動基礎架構，預設會在 /mnt 的 NVME SSD 儲存體上安裝 nfs 共用。您也可以根據效能需求，在 NVME SSD 儲存體或區塊儲存體頂端安裝自己的平行檔案系統。

### 視覺化程式節點

您可以根據需求建立視覺化程式節點，例如 GPU VM 或 BM 機器。此視覺化程式節點可以是您的堡壘主機，否則可加以區隔。視工作負載的安全需求而定，視覺化程式節點可以放置在專用或公用子網路中。

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月