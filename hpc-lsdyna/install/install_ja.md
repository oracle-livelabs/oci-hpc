# LS-DYNAのインストール

## 概要

この演習では、LS-DYNAをインストールします。

推定ラボ時間: 25分

## タスク1: バイナリのダウンロード

[LSTC Webサイト](http://www.lstc.com/download/ls-dyna)からLS-DYNAバイナリをダウンロードするか、scpを使用してマシンにプッシュできます。

MPI用に作成され、RedHat Ent Srv 5.4用にコンパイルされたバージョンを取得します。結果によると、IntelMPIはOCI上のPlatform MPIより高速に動作します。(ls-dyna\_mpp\_s\_r10\_1\_123355\_x64\_centos65\_ifort160\_avx2\_intelmpi-413 (1)。tar.gz

    <copy>
        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    </copy>
    
    

もう1つの可能性は、インストーラをオブジェクト・ストレージにアップロードすることです。

1.  コンソールのメイン・メニューで、「Object Storage」を選択します。
2.  右上にある正しいリージョンを選択してください
3.  左側の正しいコンパートメントを選択します
4.  バケットがまだ作成されていない場合は作成します
5.  バケットで、アップロード・オブジェクトを選択し、インストーラのパスを指定します。
6.  インストーラ・オブジェクトの右側にある3つのドットを選択し、「事前認証済リクエストの作成」を選択します
7.  URLを失うと元に戻せませんが、新しい事前認証済リクエストを再生成できます

インストーラからオブジェクト記憶域をダウンロードします

    <copy>
        wget PAR_URL
    </copy>
    

使用しているバージョンに応じて、インストーラを解凍するか解凍します

    <copy>
    tar -xf installer.tgz
    unzip installer.tgz
    </copy>
    

## タスク2: LS-DYNAのインストール

1.  共有場所のバイナリを解凍します。デフォルトでは、HPCクラスタはNFS共有またはGluster共有をすべてのコンピュート・ノードにマウントします。
    
        <copy>
        mkdir /mnt/nfs-share/install/lsdyna
        mv ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz /mnt/nfs-share/install/lsdyna/
        cd /mnt/nfs-share/install/lsdyna/
        tar -xf ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz
        </copy>
        
        

## タスク3: MPIライブラリのインストール

**Intel MPI 2018**

これらのコマンドはすべてのノードで実行します。

    <copy>
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo rpm --import GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo yum-config-manager --add-repo=https://yum.repos.intel.com/mpi
    sudo yum install -y intel-mpi-2018.4-057 intel-mpi-samples-2018.4-274
    </copy>
    

**プラットフォームMPI**

これらのライブラリをインストールします。

    <copy>
    sudo yum install -y glibc.i686 libgcc.x86_64 libgcc.i686
    </copy>
    

IBM Webサイトからtarファイルをダウンロードして実行します。

    <copy>
    chmod 777 platform_mpi-09.01.04.03r-ce.bin
    ./platform_mpi-09.01.04.03r-ce.bin
    </copy>
    

次に、画面の指示に従います。共有ドライブにプラットフォームをインストールすると、すべての計算ノードからアクセスできるようになります。

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月