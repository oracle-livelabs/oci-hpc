# 後処理

## 概要

この演習では、Visual Molecular Dynamics (VMD)ソフトウェアを使用して、後処理のモデルを分析します。

予想ラボ時間: 5分

## タスク: 後処理

1.  後処理では、Visual Molecular Dynamics(VMD)ソフトウェアを使用してモデルを分析できます。次のコマンドを実行して、VMDを構成します:
    
        <copy>
        ./configure
        cd src
        sudo make install
        </copy>
        
2.  vncを使用している場合は、次のようにvncserverを起動し、vncパスワードを作成します。
    
        <copy>
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
        vncserver
        vncpasswd
        </copy>
        
3.  localhost:5901 (安全なトンネリングが構成されている)を使用してvnc接続を起動し、次のコマンドを実行してVMDを起動します。
    
        <copy>
        vmd
        </copy>
        
4.  /mnt/block/work/NAMD\_modelsでapoa1およびstmv pdbファイルを開き、モデルで再生を開始します。
    

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月