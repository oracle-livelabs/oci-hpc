# STAR-CCM+のインストール

## 概要

この演習では、STAR-CCM+をインストールします。

推定ラボ時間: 25分

## タスク1: 特定のライブラリの追加

_**マーケットプレイスからCFD Ready Clusterを使用した場合、このステップは不要です**_

1.  すべてのコンピュート・ノードでOracle Linuxイメージに追加する必要があるライブラリがいくつかあります。
    
            sudo yum -y install libSM libX11 libXext libXt
        

## タスク2: バイナリのダウンロード

Siemens PLM WebサイトからSTAR-CCM+インストーラをダウンロードするか、scpを使用してマシンにプッシュできます。

        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    

もう1つの可能性は、インストーラをオブジェクト・ストレージにアップロードすることです。

1.  コンソールのメイン・メニューで、「Object Storage」を選択します。
2.  右上にある正しいリージョンを選択してください
3.  左側の正しいコンパートメントを選択します
4.  バケットがまだ作成されていない場合は作成します
5.  バケットで、アップロード・オブジェクトを選択し、インストーラのパスを指定します。
6.  インストーラ・オブジェクトの右側にある3つのドットを選択し、「事前認証済リクエストの作成」を選択します
7.  URLを失うと元に戻せませんが、新しい事前認証済リクエストを再生成できます

インストーラからオブジェクト記憶域をダウンロードします

        wget PAR_URL
    

使用しているバージョンに応じて、インストーラを解凍するか解凍します

        tar -xf installer.tgz
        unzip installer.tgz
    

## タスク3: インストール

1.  共有の場所でインストーラを起動します。デフォルトでは、HPCクラスタのすべての計算ノードにNFS共有がマウントされています。
    
            mkdir /mnt/nfs-share/install
            /path/installscript.sh -i silent -DINSTALLDIR=/mnt/nfs-share/install/
        

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月