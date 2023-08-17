# 存取您的 VNC

## 簡介

在這個實驗室中，您將存取您的 VNC。

預估實驗室時間：10 分鐘

## 作業：存取您的 VNC

1.  我們將透過 SSH 通道連線至執行處理。在您的機器上，使用下面的 ssh PORT 連線為 5900 + N 的結果編號。如果 N 的輸出為 1，則 PORT 為 5901，如果輸出為 9，則 PORT 為 5909 public\_ip 是執行 VNC 伺服器之 headnode 的公用 IP 位址。如果您使用先前的指示，連接埠將會是 5901
    
            ssh -L 5901:127.0.0.1:5901 opc@public_ip
        
2.  您現在可以使用 localhost：N 做為 VNC 伺服器，與安裝 VNC 時所設定的密碼來連線 。您可以選擇您喜歡的 VNC 用戶端，或使用本指南來安裝在您的本機機器上：
    

*   [Windows-TigerVNC](https://github.com/TigerVNC/tigervnc/wiki/Setup-TigerVNC-server-%28Windows%29)
*   [MacOS/Windows - RealVNC](https://www.realvnc.com/en/connect/download/vnc/)

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月