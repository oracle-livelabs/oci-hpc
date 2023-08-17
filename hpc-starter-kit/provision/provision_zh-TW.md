# 從 Oracle Marketplace 映像檔佈建 HPC 叢集

## 簡介

在此實驗室中，您將從 OCI Marketplace 佈建 HPC 叢集。OCI Marketplace 是各種產業解決方案的一站式平台，可輕鬆部署於我們的雲端主控台。您將可利用此市集進行自動 HPC 叢集部署。市集的樣板提供一個高效能運算解決方案的基礎，包括所有必要元件都能夠以最少的努力開始工作負載。隨著這些實驗室的進展，您將瞭解此類基準如何由第三方應用系統整合及運用，以加速您的工作流程。

預估實驗室時間：60 分鐘

### 目標

身為開發人員或資料工程師，

*   瀏覽至 OCI Marketplace
*   從 OCI 市集部署 HPC 叢集映像檔
*   檢視並執行 OCI 上的堆疊

### 必要條件

*   具有建立執行處理 VM 標準 2.1 或 BM.HPC2.36 資源配置之權限的 Oracle Cloud Infrastructure 帳戶。

## 作業 1：瀏覽至 OCI Marketplace

1.  按一下左上角的漢堡菜單 (1)。 ![市場](images/click_hamburger.png)
    
2.  向下捲動並按一下「市集」。 ![市場](images/click_marketplace.png)
    
3.  搜尋 **HPC** 的市集，然後選取 HPC 叢集。 ![市場](images/marketplace.png)
    
4.  請檢閱並接受 **Oracle 標準條款與條件**，然後按一下**啟動堆疊**。 ![市場](images/launch_stack.png)
    

## 作業 2：設定 HPC 堆疊

啟動堆疊之後，系統會將您重新導向至「建立堆疊精靈」，供您填入堆疊資訊。

1.  在**堆疊資訊**中，指定堆疊名稱， _(選擇性)_ 指定要部署在您目前不在的區間中。 ![市場](images/stack_p1.png)
    
2.  為運算資源配置和堡壘主機選取所需的 **AD** 。
    
3.  複製並貼上您的公用 **ssh 金鑰** ![市場](images/stack_p2_1.png)
    
4.  輸入叢集的**運算執行處理**數目 ![市場](images/stack_p2_2.png)
    
5.  在 \[ **網路選項** \] 底下，保留預設值 ![市場](images/stack_p2_3.png)
    
6.  按一下**下一步**以複查組態。 ![](./images/stack_p3.png)
    
7.  按一下**建立**
    

**注意：**佈建堆疊大約需要 15 分鐘。

## 作業 3：檢視執行中的堆疊

建立堆疊之後，系統會將您重新導向至可檢視堆疊工作執行的頁面。看起來應該如下所示。您可以在此處檢視：

1.  **記錄**
2.  傳遞給 Terraform 命令檔或產生的**變數**
3.  Terraform 命令檔所佈建的**關聯的資源**
4.  **輸出值**
5.  工作的**檢視狀態**

![市場](images/stack_detail_provisioning.png)

若要瀏覽至此頁面，您可以在其中檢視目前區間中的所有堆疊，請按一下左上角的漢堡功能表 ()，然後在**解決方案與平台**底下將游標暫留在**資源管理程式** > **堆疊**上。

![市場](images/nav_resource_manager.png)

全部完成！這會完成從 Oracle Marketplace Image 佈建 HPC 叢集的示範。

_您現在可以繼續進行下一個實驗室_

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者 / 日期** - Harrison Dvoor，2020 年 10 月

# <<<<<<< 標頭

> > > > > > > 上游 / 主軸