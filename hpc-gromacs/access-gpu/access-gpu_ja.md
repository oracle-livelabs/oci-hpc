# GPUノードへのアクセス

## 概要

この演習では、GPUノードにアクセスします。

予想ラボ時間: 5分

## タスク: GPUノードへのアクセス

1.  リソース・マネージャでジョブが正常に完了したら、GPUノードのパブリックIPアドレスと、左下のメニューの**「出力」**の下にある秘密キーを確認できます。
    
2.  秘密キーをローカル・マシンにコピーし、キーの権限を変更してインスタンスにログインします。
    
        chmod 400 /home/user/key
        ssh -i /home/user/key opc@ipaddress
        
        
3.  GPUノードにログインしたら、/mnt/blockからGromacsを実行できます。GPUインスタンスでGromacsを実行する方法の特定のコマンドは、README.mdファイルを参照してください。
    

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月