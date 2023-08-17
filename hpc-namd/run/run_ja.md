# アプリケーションの実行

## 概要

この演習では、アプリケーションを実行します。

予想ラボ時間: 5分

## タスク: アプリケーションの実行

提供されたterraformスクリプトを使用してアプリケーションを起動すると、CUDAを使用したNAMDが/mnt/block/NAMD/NAMD\_2.13\_CUDAフォルダにインストールされ、2つのサンプル・モデルが/mnt/block/work/NAMD\_modelsフォルダで使用できます。

1.  次のコマンドを使用して、CUDAでNAMDを実行します。
    
        <copy>
        
        namd2 +p<# of cores> +setcpuaffinity +devices <cuda visible devices> +idlepoll +commap <CPU to GPU mapping> <model path> > output.txt
        
        </copy>
        
        
    
    ここで: - +p - CPUコアの数- +setcpuaffinity - オペレーティング・システムによって番号付けされた順序でラウンドロビン方式のスレッド/プロセスを、使用可能なコアに割り当てます- +devices - GPUデバイスの数- +idlepoll - アイドル中にスリープせずにGPUをポーリング- +commap - CPUとGPUの通信マッピング- output.txt - 分析を含む出力ファイル
    
    BM.GPU2.2 の例:
    
        <copy>
        
        namd2 +p26 +devices 0,1 +idlepoll +commap 0,14 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
    
    BM.GPU3.8 の例:
    
        <copy>
        
        namd2 +p52 +devices 0,1,2,3,4,5,6,7 +commap 0,1,2,3,26,27,28,29 /mnt/block/work/NAMD_models/apoa1/apoa1.namd > output.txt
        
        </copy>
        
2.  出力ファイルに対してns\_per\_day.pyを実行して、ログに記録されたTiming文で1日当たりの平均ナノ秒数を計算します。これは、現在のCPU/GPU構成を使用してアプリケーションのパフォーマンスと効率を識別するために使用されます。
    
        <copy>
        
        ns_per_day.py output.txt
        </copy>
        

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月