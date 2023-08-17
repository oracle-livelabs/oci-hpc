# Gromacsインストーラをオブジェクト・ストレージに追加

## 概要

この演習では、Gromacsインストーラをオブジェクト・ストレージに追加します。

予想ラボ時間: 5分

## タスク: オブジェクト・ストレージへのGromacsインストーラの追加

_このgithubで提供されているterraformスクリプト[zipファイル](https://github.com/oci-hpc/oci-hpc-runbook-gromacs/tree/master/Resources/gromacs-2020.1.zip)には、Gromacs 2020.1、VMD 1.9.3のオブジェクト・ストレージURLと、[Max Planck Institute](https://www.mpibpc.mpg.de/grubmueller/bench)のベンチマーク・モデルの例がすでに含まれています。これらのURLを変更する場合は、variable.tfファイルを変更し、gromacs\_url、model\_urlおよびvisualizer\_urlの値を独自の事前認証済リクエストURLに置き換えます。_

1.  左上のメニュー![](./images/menu.png)を選択し、「オブジェクト・ストレージ」→「オブジェクト・ストレージ」を選択します。
2.  新しいバケットを作成するか、既存のバケットを選択します。作成するには、次をクリックします![](./images/create_bucket.png)
3.  デフォルトのオプション「Standard as Storage tiers」および「Oracle-Managed keys」はそのままにします。クリック![](./images/create_bucket.png)
4.  新しく作成したバケット名をクリックし、次を選択します![](./images/upload_object.png)
5.  Gromacsインストーラのtarファイルを選択し、![](./images/upload_object.png)
6.  アップロードしたオブジェクトの右側にある3つのドットをクリックし、「事前認証済リクエストの作成」を選択します
7.  次のメニューで、デフォルトのオプションをそのままにして、インストーラのURLの有効期限を選択します。クリック![](./images/pre-auth.png)
8.  次のウィンドウで、「PRE-AUTHENTICATED REQUEST URL」をコピーして保持します。このウィンドウを閉じると取得できなくなります。失うか期限切れになると、別のURLを生成する別の事前認証済リクエストを再作成することが常に可能です

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月