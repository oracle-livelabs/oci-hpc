# 存取您的叢集

## 簡介

在此實驗室中，您將存取您的叢集。

預估實驗室時間：10 分鐘

## 作業：存取您的叢集

1.  堡壘主機的公用 IP 位址可以在「輸出」左下方功能表中找到。如果您瀏覽至主功能表中的執行處理，將會同時尋找堡壘主機執行處理和公用 IP。
    
2.  也可以在該處找到要存取機器的私密金鑰。在您機器上的檔案複製文字，讓我們 say/home/user/key：
    
        chmod 600 /home/user/key 
        ssh -i /home/user/key opc@ipaddress 
        

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月