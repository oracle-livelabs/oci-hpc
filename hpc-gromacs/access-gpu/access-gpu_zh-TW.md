# 存取您的 GPU 節點

## 簡介

在此實驗室中，您將存取您的 GPU 節點。

預估實驗室時間：5 分鐘

## 作業：存取您的 GPU 節點

1.  在資源管理程式中順利完成工作之後，您可以從**輸出**底下的左下功能表找到 GPU 節點的公用 IP 位址和私密金鑰。
    
2.  將私密金鑰複製到您的本機機器上，變更金鑰的權限並登入執行處理：
    
        chmod 400 /home/user/key
        ssh -i /home/user/key opc@ipaddress
        
        
3.  登入您的 GPU 節點之後，您可以從 /mnt/block 執行 Gromac。如需如何在 GPU 執行處理上執行 Gromacs 的特定命令，請參考 README.md 檔案。
    

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月