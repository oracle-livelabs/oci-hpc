# 執行應用程式

## 簡介

在此實驗室中，您將執行應用程式。

預估實驗室時間：5 分鐘

## 作業：執行應用程式

如果使用提供的 terraform 命令檔啟動應用程式，NAMD with CUDA 會安裝在 /mnt/block/NAMD/NAMD\_2.13\_CUDA 資料夾中，而 /mnt/block/work/NAMD\_models 資料夾中則有兩種範例模型。

1.  使用 CUDA 透過下列命令執行 NAMD：
    
        <copy>
        
        namd2 +p<# of cores> +setcpuaffinity +devices <cuda visible devices> +idlepoll +commap <CPU to GPU mapping> <model path> > output.txt
        
        </copy>
        
        
    
    其中：- +p - CPU 核心數目 - +setcpuaffinity - 以循環方式將繫線 / 處理作業指定給作業系統所編號的可用核心 - +devices - GPU 裝置數目 - +idlepoll - 在閒置時輪詢 GPU 以進行結果，而不是在閒置時休眠 - +commap - CPU 與 GPU 的通訊對應 - output.txt - 具有分析的輸出檔
    
    BM.GPU2.2 的範例：
    
        <copy>
        
        namd2 +p26 +devices 0,1 +idlepoll +commap 0,14 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
    
    BM.GPU3.8 的範例：
    
        <copy>
        
        namd2 +p52 +devices 0,1,2,3,4,5,6,7 +commap 0,1,2,3,26,27,28,29 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
2.  對輸出檔案執行 ns\_per\_day.py，以計算記錄的 Timing 陳述式每天平均的奈秒數。這是用來透過目前的 CPU/GPU 組態識別應用程式的效能和效率。
    
        <copy>
        
        ns_per_day.py output.txt
        </copy>
        

## 確認

*   **作者** - 高效能運算團隊
*   **貢獻者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者 / 日期** - Samrat Khosla，2020 年 10 月