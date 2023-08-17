# 運用單一實驗室的工作坊

## 指示 - 完成時刪除此檔案

1.  開啟 Atom 或 Visual Studio 程式碼中的範例工作室樣板
2.  我們預先建立了 5 個資料夾。研討會由多個實驗室組成。
3.  移除類似以下的註解：_此實驗室的目標_
4.  請確定使用小寫資料夾和檔案名稱，並加上空格 (setup-adb NOT Setup\_ADB)
5.  您的影像名稱應具有描述性名稱。不只是 adb1、adb2、adb3。如需輔助功能，我們需要影像描述來說明影像的外觀。記住所有的小寫與破折號。
6.  從 WMS 下載我們的 QA 文件。我們發現，當您知道要向前邁進生產所需，而且使用骨架時，車間的生產速度會較快。

PS：您不需要 Readme.md。Readme 僅存在於最上層程式庫層次。我們無法追蹤 GitHub 使用狀況，因此將所有流量導向 LiveLabs. 不要建立任何直接連結到 GitHub，您的工作坊可能會很受歡迎，但我們無法追蹤，所以沒有人會知道。

## Oracle Cloud 功能表導覽的絕對路徑

**實驗室 1：佈建執行處理 -> 步驟 0：將這些標準化的 Oracle Cloud 導覽圖片 (僅供佈建使用)** \- 我們內含可供瀏覽 Oracle Cloud 功能表的通用畫面擷圖清單。請閱讀此區段並在適當時使用相關的絕對路徑影像。在 Oracle Cloud 使用者介面更新的情況下，此服務能夠為您的研討會加上未來的證據。

## 資料夾結構

在此範例中，目標是從一個較長的「上階」研討會建立數個「下階」研討會。子系由父系部份組成。

範例工作室 / -- 個別實驗室

        provision/
        setup/
        dataload/
        query/
        introduction/
          introduction.md       -- description of the everything workshop, note that it is a "lab" since there is only one
    
    workshops/
       freetier/                -- freetier version of the workshop
        index.html
        manifest.json
       livelabs/                -- livelabs version of the workshop
        index.html
        manifest.json
    

### FreeTier 與 LiveLabs 的比較

*   "FreeTier" - 包含免費試用、付費帳戶，以及某些研討會的永遠免費帳戶 (棕色按鈕)
*   "LiveLabs" - 這些研討會皆使用 Oracle 提供的租用戶 (綠色按鈕)
*   「桌面」- 這是一個新的部署，其中研習會封裝在運算執行處理中執行的 NoVNC 環境

### 關於研討會

這場研討會將包括所有 6 個單一順序的個別實驗室。

資料夾結構包括「實驗室」簡介，其中描述研習作為完整的 6 個實驗室。注意：您可能不需要為每個研討會的母語和子系版本提供不同的介紹，這僅供說明。

查看 product-name-workshop/freetier 資料夾，查看 manifest.json 檔案以查看結構。

> **備註：**在標題中使用「實驗室 n：」是選擇性的

先決條件 "lab" 是 oracle/learning-library repo 上通用資料夾中的第一個實驗室。因為此實驗室已經存在，所以您可以改用 RAW/ 絕對 URL：

    "filename": "https://oracle-livelabs.github.io/common/labs/cloud-login/cloud-login-livelabs2.md"        },
    

manifest.json 檔案需要知道每個實驗室的位置，相對於階層中的位置。在此結構中，實驗室位於上兩個階層，例如：

    "filename": "../../provision/provision.md"
    

### 例如：

這場 [APEX 研討會](https://oracle.github.io/learning-library/developer-library/apex/spreadsheet/workshops/freetier/)是一個包含以下單一實驗室的良好示範：[https://github.com/oracle-livelabs/apex/tree/main/spreadsheet](https://github.com/oracle-livelabs/apex/tree/main/spreadsheet) 。