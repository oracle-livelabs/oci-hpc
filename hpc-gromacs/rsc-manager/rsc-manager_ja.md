# リソース・マネージャ

## 概要

この演習では、新しいスタックを作成します。

予想ラボ時間: 5分

## タスク: リソース・マネージャ

OCIコンソールには、必要なすべてのリソースを作成するリソース・マネージャが使用可能です。スタックを作成するリージョンは、それがデプロイされるリージョンになります。

1.  左上のメニュー![](./images/menu.png)を選択し、「リソース・マネージャ」→「スタック」を選択します。左側のフィルタ・メニューで、スタックが実行される名前とコンパートメントを選択します。
    
2.  新規スタックの作成: ![](./images/stack.png)
    
3.  terraformスクリプトの[ZIPファイル](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip)をダウンロードし、**「構成」**を選択して**を選択します。ZIPファイル**・ボタンで**「参照」**リンクをクリックし、ダウンロードしたZIPファイルを選択します。**「選択」**をクリックします。
    
    ![](./images/zip-file.png " ")
    

[「変数の選択」](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/blob/master/Documentation/ResourceManager.md#select-variables)セクションに移動して、スタックの構成を完了します。

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月