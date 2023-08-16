# 起始環境

## 簡介

在此實驗室中，我們將複查並啟動順利執行此研討會所需的所有元件。

_預估實驗室時間：_ 30 分鐘。

### 目標

*   初始化研討會環境。

### 必要條件

此實驗室假設您具有：

*   Oracle Cloud 帳戶
*   您已完成：
    *   Lab：準備設定 (僅限_免費層_和_付費用戶_ )
    *   實驗室：環境設定

> **注意：** _使用方便的複製 / 貼上功能_ **複製** _在整個指南中使用時，您必須在貼上後按_ **ENTER** _鍵。否則，最後一行將保留在緩衝區中，直到您按下_ **ENTER 為止！**

## 作業 1：驗證必要的處理作業是否已啟動並在執行中。

1.  現在開始存取您的遠端桌面階段作業，請依照下列指示繼續執行您的環境，再開始執行後續的實驗室。下列處理應該啟動並在執行中：
    
    *   資料庫監聽器
        *   監聽器 (1521)
        *   LISTCDB2 (1522)
    *   資料庫伺服器執行處理
        *   CDB1
        *   CDB2
    
    您可以按一下資料庫旁的 _+_ 符號來測試資料庫連線，如 _SQL Developer Oracle Connections_ 面板所示。
    
    ![](./images/19c_hol_landing.png " ")
    
2.  按一下桌面上的_終端機_圖示以啟動作業階段，然後執行下列以驗證預期的處理作業是否已啟動。
    
        <copy>
        ps -ef|grep LIST|grep -v grep
        ps -ef|grep ora_|grep pmon|grep -v grep
        systemctl status oracle-database
        systemctl status oracle-db-listener
        </copy>
        
    
    ![](./images/check-pmon-up.png " ") ![](./images/check-db-service-up.png " ") ![](./images/check-dblistner-service-up.png " ")
    
    如果輸出中顯示所有預期的處理作業，如上所示，您的環境便已準備好進行下一個作業。
    
3.  如果您看到有問題的輸出、失敗或停用中的元件，請據此重新啟動服務。
    
        <copy>
        sudo systemctl restart oracle-database
        sudo systemctl restart oracle-db-listener
        </copy>
        

## 作業 2：初始化多用戶使用案例的資料庫

1.  以 _oracle_ 使用者身分從遠端桌面階段作業執行下方的區塊，以重新整理實驗室使用者自建物件
    
        <copy>
        cd ~
        wget -O labs-novnc.zip https://objectstorage.us-ashburn-1.oraclecloud.com/p/jyHA4nclWcTaekNIdpKPq3u2gsLb00v_1mmRKDIuOEsp--D6GJWS_tMrqGmb85R2/n/c4u04/b/livelabsfiles/o/labfiles/labs-novnc.zip
        unzip -qo labs-novnc.zip
        rm -f labs-novnc.zip
        cd labs/multitenant
        chmod +x *.sh
        </copy>
        
    
    ![](./images/init-multitenant.png " ")
    

您現在可以[進入下一個實驗室](#next)。

## 附錄 1：管理啟動服務

1.  資料庫服務 (所有資料庫和標準監聽器)。
    
    *   開始
    
        <copy>
        sudo systemctl start oracle-database
        </copy>
        
    
    *   停止
    
        <copy>
        sudo systemctl stop oracle-database
        </copy>
        
    
    *   狀態
    
        <copy>
        systemctl status oracle-database
        </copy>
        
    
    *   重新開始
    
        <copy>
        sudo systemctl restart oracle-database
        </copy>
        
2.  資料庫服務 (非標準監聽器)。
    
    *   開始
    
        <copy>
        sudo systemctl start oracle-db-listener
        </copy>
        
    
    *   停止
    
        <copy>
        sudo systemctl stop oracle-db-listener
        </copy>
        
    
    *   狀態
    
        <copy>
        systemctl status oracle-db-listener
        </copy>
        
    
    *   重新開始
    
        <copy>
        sudo systemctl restart oracle-db-listener
        </copy>
        

## 確認

*   **作者** - Andy Rivenes，Oracle Database In-Memory 資深首席產品經理
*   **貢獻者** - Kay Malcolm、Didi Han、Rene Fontcha
*   **上次更新者 / 日期** - 北美科技公司 LiveLabs 平台主管 Rene Fontcha，2021 年 10 月