# 準備設定

## 簡介

這個實驗室將會示範如何下載必要的 Oracle Resource Manager (ORM) 堆疊壓縮檔，以設定此研討會所需的資源。

_預估實驗室時間：_ 10 分鐘

### 目標

*   下載 ORM 堆疊
*   設定現有的虛擬雲端網路 (VCN)

### 必要條件

此實驗室假設您具有：

*   Oracle Cloud 帳戶

## 作業 1：下載 Oracle Resource Manager (ORM) 堆疊壓縮檔

1.  按一下下方連結以下載建立環境所需的資源管理程式壓縮檔：
    
    _注意事項 1：_如果提供研討會的單一堆疊下載，請使用此簡單表示式。
    
    *   [範例 -mkplc-freetier.zip](https://objectstorage.us-ashburn-1.oraclecloud.com/p/clKCvIhItftqqFoXdipFq5oukh0jVuwcKEMHVdqOPXN7oUqaoGwPZsimi5pgpfpR/n/natdsecurity/b/stack/o/sample-mkplc-freetier.zip)
    
    _備註 2：_如果針對相同的研習提供多個堆疊下載，請使用類似下面的條件運算式。請記住，條件或 _type_ 必須與 _manifest.json_ 檔案中的有效項目配對。請參閱 _freetier-advanced_ 與 _freetier-basics_
    

\- \[sample-mkplc-advanced.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/\_EIwsXv5v6KkKcQldUQixExqAgJCbY826XovJec4I25rc4dHEZW4whrF-nb2QUye/n/natdsecurity/b/stack/o/sample-mkplc-advanced.zip) \- \[sample-mkplc-basics.zip\](https://objectstorage.us-ashburn-1.oraclecloud.com/p/m4wcgeN1hw9D1zV3pgOkbRjwanAt5dIW7QsZS7znZNnHU63vh495UHhkiRtaDJHE/n/natdsecurity/b/stack/o/sample-mkplc-basics.zip)

2.  儲存至您的下載項目資料夾。

強烈建議使用此堆疊在您的執行處理建立獨立 / 專用的 VCN。請跳至_任務 3_ 以追蹤我們的建議。如果想要使用現有的 VCN，請繼續下一個作業，以必要的傳入規則更新現有的 VCN。

## 作業 2：將安全規則新增至現有的 VCN

此研討會需要使用特定數量的連接埠，並要求使用建立專用 VCN 的預設 ORM 堆疊執行來滿足此要求。若要使用現有的 VCN/ 子網路，必須將下列規則新增至安全清單。

| 類型 | 來源連接埠 | 來源 CIDR | 目標連接埠 | 通訊協定 | 描述 |
| :-- | :-: | :-: | :-: | :-: | :-- |
| 傳入 | 全部 | 0.0.0.0/0 | 22 日 | TCP | SSH |
| 傳入 | 全部 | 0.0.0.0/0 | 80 歲 | TCP | 使用 noVNC 的遠端桌面 |
| 傳出 | 全部 | 不適用 | 80 歲 | TCP | 外送 HTTP 存取 |
| 傳出 | 全部 | 不適用 | 443 年 | TCP | 外送 HTTPS 存取 |
| {：title=「必要網路安全規則清單」} |  |  |  |  |  |

1.  前往_網路 >> 虛擬雲端網路_
2.  選擇網路
3.  在資源下方，選取安全性清單
4.  按一下「建立安全清單」按鈕底下的「預設安全清單」
5.  按一下「新增傳入規則」按鈕
6.  輸入下列資訊：
    *   來源類型：CIDR
    *   來源 CIDR：0.0.0.0/0
    *   IP 協定：TCP
    *   來源連接埠範圍：全部 (保留預設值)
    *   目的地連接埠範圍：_從上表選取_
    *   描述：_從上面表格中選取對應的描述_
7.  按一下「新增輸入規則」按鈕
8.  重複步驟 \[5-7\]，直到為表格中所列的每個連接埠建立規則為止

## 任務 3：設定運算

使用上述兩項工作的詳細資訊，前往_環境設定_實驗室，使用 Oracle Resource Manager (ORM) 和下列其中一個選項設定您的研討會環境：

*   建立堆疊：_運算 + 網路_
*   建立堆疊：使用現有 VCN 進行_僅限運算_，根據上述_任務 2_ 的安全清單已更新

您現在可以繼續下一個實驗室。

## 確認

*   **作者** - 北美科技公司 LiveLabs 平台主管 Rene Fontcha
*   **貢獻者** - Meghana Banka
*   **上次更新者 / 日期** - Rene Fontcha，LiveLabs 平台主管，北美技術，2022 年 8 月