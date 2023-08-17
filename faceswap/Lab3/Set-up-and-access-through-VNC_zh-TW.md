# 透過 VNC 設定與存取

## 簡介

在此實驗室中，我們將逐步介紹透過 VNC 用戶端設定與存取 Ubuntu 實例的必要步驟 (此處：TigerVNC)。

預估車間時間：15 分鐘

### 目標

*   透過 SSH 連線至您的運算執行處理
*   在您的 Ubuntu 執行處理上設定並設定 VNC
*   透過 VNC 從屬端存取您的運算執行處理 (例如 TigerVNC)

### 您需要什麼？

*   完整的實驗室 1 和實驗室 2
*   [新增傳入規則至 VCN 安全清單](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm#Using)以允許連接埠 5900+N 的 TCP 流量，其中 N 是顯示號碼 (通常為實體顯示畫面的 ：0)。![](images/vncseclist.PNG " ")

## 任務 1：透過 SSH 連線至您的運算執行處理

1.  開啟您選擇的終端機 (也稱為命令列或主控台) 。在本範例中，我們使用本機 Windows 機器，並使用 Windows PowerShell 作為終端機。
    
2.  請注意，您將在區間執行處理清單的**運算 -> 執行處理**底下找到之 Ubuntu 執行處理的公用 IP 位址。
    
3.  在您的終端機中，瀏覽到您建立 Ubuntu 運算執行處理時所使用 SSH 金鑰組所在的資料夾。在我們的課程中，資料夾稱為 _ssh_ 。
    

       <copy>cd .ssh</copy>
    

4.  輸入下列命令以存取您的 Ubuntu 運算執行處理，您必須將 _private-key_ 取代為您的 OpenSSH 私密金鑰和 _public ip_ 取代為 Ubuntu 執行處理的公用 IP 位址。

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

## 作業 2：在 Ubuntu 執行處理上設定並設定 VNC

1.  在您的終端機內執行以下命令，以安裝 VNC 伺服器：

       <copy>sudo apt update</copy>
    

    <copy>sudo apt -y install vnc4server</copy>
    

2.  安裝桌面環境。我們將會安裝 Xfce 桌面環境 (但您可以藉由輸入下列指令來選擇使用其他桌面環境)：

       <copy>sudo apt install xfce4 xfce4-goodies</copy>
    

3.  執行 vncpasswd 指令，為您的 VNC 伺服器設定密碼。

       <copy>vncpasswd</copy>
    

系統出現提示時，請輸入您的密碼，並第二次輸入以驗證密碼。

4.  使用下列指令啟動 VNC 伺服器：

       <copy>vncserver :1</copy>
    

您會收到下列輸出。 ![](images/vncserver.PNG " ")

5.  使用下列指令終止 VNC 伺服器：

       <copy>vncserver -kill :1</copy>
    

![](images/vnc-kill.PNG " ")

6.  VNC 配置檔案位於 ~/.vnc/xstartup。使用您最愛的文字編輯器進行編輯。我們將使用 vim。

       <copy>sudo vim ~/.vnc/xstartup</copy>
    

7.  在檔案的結尾新增下列行：

       <copy>exec /usr/bin/startxfce4 &</copy>
    

如果您使用 vim：按 **A** 在結尾附加文字，然後複製貼上上述指令。按 ESC 鍵切換到指令模式 。鍵入 **：wq** - 以儲存並結束。

8.  執行下列指令以啟動 VNC 伺服器。

       <copy>vncserver :1</copy>
    

![](images/startvncserver.PNG " ")

9.  執行下列命令以關閉執行處理的 SSH 連線：

       <copy>exit</copy>
    

## 作業 3：連線至 VNC 桌面

您可以使用 VNC 從屬端 (例如 TigerVNC) 和 SSH 通道連線至遠端桌面。

1.  使用下列命令建立 VNC 伺服器的 SSH 通道 (私密金鑰為您的私密 OpenSSH 金鑰，public\_ip 則為來自 Ubuntu 運算執行處理的專用 IP 位址)：

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  安裝 vncviewer 從屬端，在我們的情況下將會是 TigerVNC。

       <copy>sudo apt install tigervnc-viewer</copy>
    

3.  在您的本機機器上下載並安裝 VNC 用戶端 (例如 TigerVNC)。使用您的 VNC 用戶端連線至 **127.0.0.1：5901** 。 ![](images/tigervnc.PNG " ")
    
4.  在系統提示輸入密碼時，請輸入 VNC 伺服器組態期間建立的密碼。 ![](images/tigervncpwd.PNG " ")
    

您的遠端 Ubuntu 桌面將開啟。 ![](images/vncdesktop.PNG " ")

## **確認**

*   **建立者 / 日期** - Maria Patelkou，HPC Solution Architect，Oracle Proposal to Production Program，2021 年 3 月
*   **上次更新者 / 日期** - Maria Patelkou，HPC Solution Architect，Oracle Proposal to Production Program，2021 年 3 月