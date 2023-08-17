# 後續處理

## 簡介

在此實驗室中，您將使用 Visual Molecular Dynamics (VMD) 軟體分析後處理的模型。

預估實驗室時間：5 分鐘

## 作業：後續處理

1.  對於後處理，您可以使用 Visual Molecular Dynamics (VMD) 軟體分析模型。執行下列命令來設定 VMD：
    
        <copy>
        ./configure
        cd src
        sudo make install
        </copy>
        
2.  如果您使用 vnc，請啟動 vncserver 並建立 vnc 密碼，如下所示：
    
        <copy>
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
        vncserver
        vncpasswd
        </copy>
        
3.  使用 localhost:5901 啟動 vnc 連線 (已設定安全通道)，然後執行下列命令來啟動 VMD：
    
        <copy>
        vmd
        </copy>
        
4.  開啟 /mnt/block/work/NAMD\_models 中的 apoa1 和 stmv pdb 檔案，然後開始使用您的模型播放。
    

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月