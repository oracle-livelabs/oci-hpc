# 変数を選択

## 概要

この演習では、スタックの変数を入力します。

予想ラボ時間: 10分

## タスク: 変数の選択

1.  ![](./images/next.png)をクリックし、変数を入力します。
    
    **GPUノード**:
    
    *   SHAPE OF THE GPU COMPUTE NODE: Gromacsの実行に使用される計算ノードのシェイプ。最高のパフォーマンスを実現するには、ベア・メタル・シェイプBM.GPU2.2またはBM.GPU3.8を選択します。
    *   可用性ドメイン: インスタンスおよびブロック・ボリュームのAD。ADには、使用可能なGPUが必要です。
    *   GPUノード数: GPUノード数が必要です。
    *   GPUのVNCタイプ: ヘッドノードのビジュアライゼーション・タイプ: none、VNCまたはX11VNC。
    *   PASSWORD FOR THE VNC SESSIONS: Pre/Post NodeでVNCセッションを使用するパスワード。
    
    **ブロック・オプション**:
    
    *   BLOCK VOLUME SIZE (GB): 共有ブロック・ボリュームのサイズ。
    
    **グロマックス**:
    
    *   GromacsをダウンロードするURL: Gromacs 2020.1でコンパイルされたバイナリのURL (URLを別のバージョンに置き換えるか、後でダウンロードする場合は空白のままにします)。
    *   MODEL TARBALLをダウンロードするURL: 実行するモデルのURL (URLを別のモデルに置き換えるか、後でダウンロードする場合は空白のままにします)。
    *   VMD VISUALIZATION SOFTWAREをダウンロードするURL: VMD 1.9.3をダウンロードしてGromacsモデルを視覚化するURL(URLを別のビジュアライゼーション・ソフトウェアに置き換えるか、後でダウンロードする場合は空白のままにします)。
2.  ![](./images/next.png)をクリックします。情報を確認し、 ![](./images/create.png)
    

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月