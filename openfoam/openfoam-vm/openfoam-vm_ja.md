# OCIでのStandard VMでのOpenFoamプロジェクトの実行

## 概要

次のタスクは、Oracle Cloud InfrastructureのOpenFOAM CFDソフトウェアの評価を支援するように設計されています。タスクを完了して、OpenFOAMを自動的にダウンロードおよび構成します。OpenFOAMは、2004年以降主にOpenCFD Ltdがリリースおよび開発した無料のオープン・ソースCFDソフトウェアです。商業組織と学術組織の両方から、エンジニアリングおよび科学のほとんどの領域に大規模なユーザー基盤があります。OpenFOAMには、化学反応、乱流および熱伝達を含む複雑な流体フローから音響学、固体機械学、電磁学に至るまで、様々な機能があります。

基本的なVM.Standard 2.1シェイプでのOpenFoamのインストールを順を追って説明しますが、実際のデプロイメントではBM.HPC2.36のような大きいシェイプが適しています。高パフォーマンス・シェイプの中には、特定のリージョンとアベイラビリティ・ドメインでのみ使用可能なものがあります。

パブリック・サブネット上のクラスタのヘッドノードとプライベート・サブネット内のワーカー・コンピュート・ノードの2つのノードを作成します。ワーカー・ノードにアクセスするには、まずヘッドノードを作成し、ヘッドノードにSSHキーを生成し、ワーカー・ノードの作成時にその公開キーを使用します。

**問題を記録**するには、[ここ](https://github.com/oracle/learning-library/issues/new)をクリックして、github oracle repository issue submitフォームに移動します。

見積時間: 60分

### 目的

開発者のデータエンジニアとして、

1.  High Performance Computingインスタンスを手動でデプロイ
    
2.  クラスタ・ネットワークの作成
    
3.  ネットワーク・ファイル・システムの作成
    
4.  VNCの設定
    
5.  OpenFoamおよびParaviewのインストール
    

### 前提条件

*   インスタンスのVM標準2.1またはBM.HPC2.36シェイプを作成する権限を持つOracle Cloud Infrastructureアカウント。

### インフラストラクチャの用語

*   ワーカー・ノード: 計算シミュレーションまたはその他のエンジニアリング・ワークロードのワークロードを実行するための処理能力を提供するHPCコンピュート・インスタンス。このデモ・コンピュート・シェイプでは、BM.HPC2.36ノードがワーカー・ノードです。
    
*   ヘッド・ノード: すべての計算ジョブをスケジュールしてワーカー・ノードで実行するために発行できるコンピュート・インスタンス。HeadノードとBastionノードは、同じマシンにすることができます。このデモでは、要塞ノードをプロビジョニングしています。
    
*   要塞ノード: パブリックIPを持つコンピュート・インスタンスは、通常プライベート・ネットワークにあるワーカー・ノードに接続するエントリ・ポイントとして機能します。
    
*   RoCEネットワーク: RDMA over Converged Ethernet (RoCE)は、Ethernetネットワークを介したリモート・ダイレクト・メモリー・アクセス(RDMA)を可能にするネットワーク・プロトコルです。
    

## タスク1: Oracle Virtual Cloud Networkのプロビジョニング

1.  インスタンスを作成する前に、Virtual Cloudネットワークを構成する必要があります。左上のメニューを選択し、**「ネットワーキング」**および**「Virtual Cloud Networks」**を選択します
    
    ![](./images/vcn.png " ")
    
    ![](./images/create_vcn.png " ")
    
    ![](./images/vcn_content.png " ")
    
2.  次のページで、VCNについて次を選択します:
    
    *   名前
        
    *   コンパートメント
        
    *   CIDRブロック(例: 10.0.0.0/16)
        
3.  最下部にスクロールし、「VCNの作成」をクリックします
    
4.  サブネットの作成
    
    1.  パブリック・サブネット
        
        *   名前: hpc\_public
            
        *   サブネット・タイプ: リージョン
            
        *   CIDRブロック: 10.0.0/24
            
        *   Route Table: 「Default Route table」
            
        *   サブネット・アクセス: パブリック・サブネット
            
        *   Security List: 「Default Security List」
            
    2.  プライベート・サブネット
        
        *   名前: hpc\_private
            
        *   サブネット・タイプ: リージョン
            
        *   CIDRブロック: 10.0.3.0/24
            
        *   Route Table: 前のステップで作成した「Route Table」を選択します。
            
        *   サブネット・アクセス: プライベート・サブネット
            
        *   Security List: 前のステップで作成したセキュリティ・リストを選択します。
            
5.  **「サブネットの作成」**をクリックします
    
    ![](./images/create_subnet.png " ")
    
    ![](./images/create_subnet_content.png " ")
    
6.  作成した`hpc_vcn`をクリックし、ページの左側にある**「リソース」**メニューで**「インターネット・ゲートウェイ」**を選択して、インターネット・ゲートウェイを作成します。
    
    ![](./images/create_IG.png " ")
    
    ![](./images/create_IG_content.png " ")
    
    > **ノート:**これによりインターネット・ゲートウェイが作成され、ルート表に関連付ける必要があります。この場合、デフォルト・ルート表はパブリック・サブネットに使用されるため、インターネット・ゲートウェイはそのルート表に関連付ける必要があります。
    
7.  ルート表にルート・ルールを追加します。ページの左側にある**「リソース」**メニューで、**「`hpc_vcn`のデフォルト・ルート表」**を選択し、**「ルート・ルールの追加」**をクリックします
    
    *   ターゲット・タイプ: Internet Gateway
        
    *   宛先CIDRブロック: 0.0.0.0/0
        
    *   コンパートメント内のターゲット・インターネット・ゲートウェイ: 作成したインターネット・ゲートウェイ
        
    
    ![](./images/route_table.png " ")
    
    ![](./images/route_rules.png " ")
    

## タスク2: クラスタ・ノードの作成

このタスクには、パブリック・サブネット上のクラスタの**Headnode**とプライベート・サブネットの**ワーカー・コンピュート・ノード**の2つのノードを作成します。ワーカー・ノードにアクセスするには、まずヘッドノードを作成し、ヘッドノードにSSHキーを生成し、ワーカー・ノードの作成時にその公開キーを使用します。

> **ノート:**このタスクでは、基本的なVM.Standard2.1シェイプのみを使用しますが、実際のデプロイメントではBM.HPC2.36のような大きいシェイプが適切です。高パフォーマンス・シェイプの中には、特定のリージョンとアベイラビリティ・ドメインでのみ使用可能なものがあります。

1.  ヘッドノードの作成
    
    *   名前: hpc\_head
        
    *   イメージまたはオペレーティング・システム: 最新バージョンのOracle Linux(デフォルト)
        
    *   Availability Domain: VM.Standard 2.1シェイプで使用可能なドメイン
        
    *   インスタンスのシェイプ:
        
        *   VM.Standard 2.1
            
        *   その他のシェイプ(使用可能な場合はBM.HPC2.36を優先)
            
    *   Virtual Cloud Network: 以前に作成されたVCN
        
    *   Subnet: 前に作成されたパブリック・サブネット
        
    *   SSH鍵: 公開鍵ファイルを添付します。
        
    
    1.  左上のメニューで、「Compute」を選択してインスタンスを作成します。
        
        ![](./images/compute.png " ")
        
        ![](./images/compute_bm.png " ")
        
        ![](./images/compute_vm.png " ")
        
    2.  ヘッドノードにSSH接続し、クラスタが通信するためのSSHキーを生成します。
        
            $ <copy>ssh -i <ssh_key> opc@<headnode_ip_address></copy>
            
            $ <copy>ssh-keygen</copy>
            
        
        > **ノート:**パスフレーズについて尋ねられた場合は、sshキー・ファイルの場所(/home/opc/.ssh/id\_rsa)を変更せず、\[Enter\]を押します(2回)。
        
        ワーカー・ノードの作成に使用される文字列全体を実行してコピーします。
        
            $ <copy>cat ~/.ssh/id_rsa.pub</copy>
            
2.  ワーカー・ノードの作成
    
    1.  左上のメニューで「Compute」を選択し、次の詳細を含むインスタンスを作成します:
        
        *   名前: hpc\_worker1
            
        *   イメージまたはオペレーティング・システム: 最新バージョンのOracle Linux(デフォルト)
            
        *   Availability Domain: VM.Standard 2.1シェイプで使用可能なドメイン
            
        *   インスタンスのシェイプ:
            
            *   VM.Standard 2.1
                
            *   その他のシェイプ(使用可能な場合はBM.HPC2.36を優先)
                
        *   Virtual Cloud Network: 前に作成されたVCN
            
        *   Subnet: 前に作成されたプライベート・サブネット
            
        *   SSHキー: 上の1つのステップからコピーされた公開キー文字列。
            
    2.  ワーカー・ノードにSSH接続します。ヘッド・ノードにログインしたコンソールに戻り、プライベートIPアドレスとSSHをヘッド・ノードからワーカー・ノードに取り込みます。
        
            $ <copy>ssh opc@10.x.x.x</copy>
            

## タスク3: NAT Gatewayの設定

> **ノート:**これはワーカー・ノード専用です。

1.  ワーカー・ノードを選択し、左側の**「リソース」**メニューで**「アタッチされたVNIC」**をクリックします
    
2.  **「VNICの編集」**を選択します
    
3.  **「ソース/宛先チェックのスキップ」**を選択解除し、**「VNICの更新」**をクリックします
    
    ![](./images/nat_gateway.png " ")
    

## タスク4: ドライブのマウント

> **ノート:**ノード・シェイプにNVMeがアタッチされている(BM.HPC2.36にVM.Standard2.1ではなく1つある)場合にのみ、HPCマシンにはローカルのNVMeストレージがありますが、デフォルトではマウントされていません。VM.Standard2.1を使用している場合はステップ5にスキップします

1.  ヘッドノードへのSSHを実行し、次のコマンドを実行します
    
        $ <copy>lsblk</copy>
        
    
    ドライブは左側のNAMEで一覧表示されるはずです(異なる場合は、次のコマンドで変更してください)。nvme0n1である可能性があります。
    
    ヘッドノードには、インストールおよびモデルとの共有ドライブが含まれます。これは、異なるすべてのワーカー・ノード間で共有されます。各ワーカー・ノードは、NVMeドライブでローカルに実行されているドライブもマウントします。この例では、共有ディレクトリは500 GBですが、自由に変更できます。
    
2.  ワーカー・ノードでドライブをパーティション化します(オプション)。
    
        $ <copy>sudo yum -y install gdisk</copy>
        
    
        $ <copy>sudo gdisk /dev/nvme0n1</copy>     $ > n    # Create new partition
        $ > 1    # Partition Number
        $ >      # Default start of the partition
        $ > +500G # Size of the shared partition
        $ > 8300  # Type = Linux File System
        $ > n     # Create new partition
        $ > 2     # Partition Number
        $ >       # Default start of the partition
        $ >       # Default end of the partition, to fill the whole drive
        
        $ > 8300  # Type = Linux File System
        
        $ > w      # Write to file
        $ > Y      # Accept Changes
        
3.  ワーカー・ノードのドライブのフォーマット
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1</copy>
        
4.  headnodeのパーティションを書式設定します。
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p1</copy>
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p2</copy>
        
5.  ディレクトリを作成し、ドライブをマウントします
    
6.  Headnode(localおよびshare): 500Gパーティションのマウント・ディレクトリとして/mnt/shareを選択し、大きいパーティションの場合は/mnt/localを選択します。
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1p1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1p2 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        
7.  ヘッドノード(共有):
    
        $ <copy>sudo mkdir /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        
8.  ワーカー・ノード: ドライブ全体のマウント・ディレクトリとして/mnt/localを選択します。
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        

## タスク5: ネットワーク・ファイル・システムの作成

1.  マウント・ターゲットを作成します。左上のメニューで、**「File Storage」**および**「Mount Target」**をクリックします。
    
    *   新規マウント・ターゲット名: 名前を入力します(例: openfoam\_fs)
        
    *   Virtual Cloud Network: 上で作成したVCNを選択します。
        
    *   Subnet: パブリックVCNを選択します。
        
2.  Headnodeはパブリック・サブネット内にあります。ファイアウォールを稼働させ、次の方法で例外を追加します。  
    
        <copy>sudo firewall-cmd --permanent --zone=public --add-service=nfs</copy>
        <copy>sudo firewall-cmd --reload</copy>
        
3.  作成されたマウント・ターゲットをクリックし、エクスポート・パスをクリックしてから、マウント・コマンドをクリックし、次のようなコマンドをコピーします:
    
        <copy>sudo yum install nfs-utils</copy>
        <copy>sudo mkdir -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        
4.  ワーカー・ノードでは、アクセスを制限するセキュリティ・リストがあるプライベート・サブネットにあるため、ファイアウォールを完全に無効にできます。次に、nfs-utilsツールをインストールし、前述のようにnfsをマウントできます。
    
        <copy>sudo systemctl stop firewalld</copy>
        <copy>sudo yum -y install nfs-utils</copy>
        <copy>sudo mkdir  -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        

## タスク6: OpenFOAMのインストール

1.  **すべてのワーカー・ノードの接続**  
    
    各ワーカー・ノードは、すべてのワーカー・ノードと通信できる必要があります。SSH通信は機能しますが、すべてのホストが既知のホストファイルにない場合、ほとんどのアプリケーションに問題があります。VCN内のアドレスを持つノードの既知のホスト・チェックを無効にするには、次のコマンドで非アクティブ化できます。サブネット内のアドレスが異なる場合は、少し変更する必要がある場合があります。次のコード・ブロックをシェル・スクリプトに置き、スクリプトを実行します。
    
        <copy>for i in 0 1 2 3
        do
             echo Host 10.0.$i.* | sudo tee -a ~/.ssh/config
             echo "    StrictHostKeyChecking no" | sudo tee -a ~/.ssh/config
        done</copy>
        
2.  **機械リストの作成**  
    
    ヘッドノード上の OpenFOAMは、使用可能な計算ノードを自動的に認識しません。マシン・ファイルは、すべてのノードのプライベートIPアドレスおよび使用可能なCPUの数を`/mnt/share/machinelist.txt with`に作成できます。ヘッドノードも含める必要があります。エントリの書式は`private-IP-address cpu=number-of-cores`です
    
        <copy>10.0.0.2 cpu=1
        10.0.3.2 cpu=1</copy>
        
3.  **ヘッドノード**
    
    ソースからインストールし、次のコマンドのtarballsへのパスを変更します。この例では、基盤OpenFOAMソースがあります。ESIのOpenFOAMもテストされています。異なる計算ノード間でインストールを共有するには、ネットワークファイルシステムにインストールします。
    
        <copy>$ sudo yum groupinstall -y 'Development Tools'
        $ sudo yum -y install devtoolset-8 gcc-c++ zlib-devel openmpi openmpi-devel
        $ cd /mnt/share
        $ wget -O - http://dl.openfoam.org/source/7 | tar xvz
        $ wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
        $ mv OpenFOAM-7-version-7 OpenFOAM-7
        $ mv ThirdParty-7-version-7 ThirdParty-7
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | sudo tee -a ~/.bashrc
        $ echo export $ LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc
        $ cd /mnt/share/OpenFOAM-7
        $ ./Allwmake -j</copy>
        
4.  **ワーカー・ノード**  
    
        $ <copy>sudo yum -y install openmpi openmpi-devel
        $ cd /mnt/share
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | $ sudo tee -a ~/.bashrc
        $ echo export $LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | $ sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc</copy>
        

## タスク7: シミュレーション・ワークロードの実行および出力のレンダリング

1.  Headnodeで、Paraviewパッケージを使用して出力をレンダリングするために必要な次のコマンドを実行します。
    
        $ <copy>sudo yum install -y mesa-libGLU
        $ cd /mnt/share
        $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" $ https://www.paraview.org/paraview-downloads/download.php >  file.tar.gz
        $ tar -xf file.tar.gz</copy>
        
2.  headnondeで、次のコマンドを実行してVNCサーバーを設定します:  
    
        $ <copy>sudo yum -y groupinstall 'Server with GUI'
        $ sudo yum -y install tigervnc-server mesa-libGL
        $ sudo mkdir /home/opc/.vnc/
        $ sudo chown opc:opc /home/opc/.vnc
        $ echo "HPC_oci1" | vncpasswd -f > /home/opc/.vnc/passwd
        $ chown opc:opc /home/opc/.vnc/passwd
        $ chmod 600 /home/opc/.vnc/passwd
        $ /usr/bin/vncserver</copy>
        
3.  ワーカー・ノードの1つで、/mnt/share/workの[スクリプト](../scripts/motorbike_RDMA.tgz)を使用してこのzipをダウンロードします。`tar -xf motorbike_RDMA.tgz`を使用してファイルを解凍します
    
4.  ワークロードを実行する前に、構築したアーキテクチャに適合するようにallrunファイルを編集する必要があります。まず、OpenFOAMインストーラ・フォルダからフォルダを移動します
    
        $ <copy>model_drive=/mnt/share
        $ sudo mkdir $model_drive/work
        $ sudo chmod 777 $model_drive/work
        $ cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
        $ cd /mnt/share/work/motorBike/system</copy>
        
5.  ファイル・システム/decomposeParDictを編集し、この行numberOfSubdomains 6、numberOfSubdomains 12、または必要なプロセス数を変更します。次に、hierarchicalCoeffsブロックで、nをn (3 2 1)からn (4 3 1)に変更します。これらの3つの値を乗算すると、numberOfSubdomainsを取得する必要があります
    
    1つのVM.Standard2.1ワーカー・ノードの構成で実行する場合:
    
        =========                 |
        \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
         \\    /   O peration     | Website:  https://openfoam.org
          \\  /    A nd           | Version:  7
           \\/     M anipulation  |
        \*---------------------------------------------------------------------------*/
        FoamFile
        {
             version     2.0;
             format      ascii;
             class       dictionary;
             object      decomposeParDict;
        }
        
        // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //
        
        numberOfSubdomains 2;
        
        method          hierarchical;
        // method          ptscotch;
        
        simpleCoeffs
        {
             n               (4 1 1);
             delta           0.001;
        }
        
        hierarchicalCoeffs
        {
             n               (2 1 1);
             delta           0.001;
             order           xyz;
        }
        
        manualCoeffs
        {
             dataFile        "cellDecomposition";
        }
        
        // ************************************************************************* //
        
6.  次に、/mnt/share/work/motorBikeのAllrunファイルを次のように編集します。
    
        <copy>#!/bin/sh
        cd ${0%/*} || exit 1    # Run from this directory
        NP=$1
        install_drive=/mnt/share
        # Source tutorial run functions
        . $WM_PROJECT_DIR/bin/tools/RunFunctions
        
        # Copy motorbike surface from resources directory
        cp $FOAM_TUTORIALS/resources/geometry/motorBike.obj.gz constant/triSurface/
        cp $install_drive/machinelist.txt hostfile
        
        runApplication surfaceFeatures
        
        runApplication blockMesh
        
        runApplication decomposePar -copyZero
        echo "Running snappyHexMesh"
        mpirun -np $NP -machinefile hostfile snappyHexMesh -parallel -overwrite > log.snappyHexMesh
        ls -d processor* | xargs -I {} rm -rf ./{}/0
        ls -d processor* | xargs -I {} cp -r 0 ./{}/0
        echo "Running patchsummary"
        mpirun -np $NP -machinefile hostfile patchSummary -parallel > log.patchSummary
        echo "Running potentialFoam"
        mpirun -np $NP -machinefile hostfile potentialFoam -parallel > log.potentialFoam
        echo "Running simpleFoam"
        mpirun -np $NP -machinefile hostfile $(getApplication) -parallel > log.simpleFoam
        
        runApplication reconstructParMesh -constant
        runApplication reconstructPar -latestTime
        
        foamToVTK
        touch motorbike.foam</copy>
        
7.  ワーカー・ノードにいることを確認し、ワークロードを実行します。
    
        $ <copy>ssh worker_node_IP
        $ cd /mnt/share/work/
        $ ./Allrun 2
        
        $ ./Allrun 2
        $ Cleaning /mnt/share/work case
        $ Mesh Dimensions: (40 16 16)
        $ Cores:36: 6, 6, 1
        $ Running surfaceFeatures on /mnt/share/work
        $ Running blockMesh on /mnt/share/work
        $ Running decomposePar on /mnt/share/work
        $ Running snappyHexMesh
        $ Running patchsummary
        $ Running potentialFoam
        $ Running simpleFoam
        $ Running reconstructParMesh on /mnt/share/work
        $ Running reconstructPar on /mnt/share/work
        219.95</copy>
        
8.  ワークロードが正常に完了したら、次のようにマシンでVNCクライアントを構成します。要塞サーバーおよびVNCポートのパブリックIPの指定
    
    ![](./images/24-VNC_Connection.png " ")
    
9.  オプション: VNCポート5901を開けない場合、またはセキュリティー上の理由によりこのポートのsshトンネルを作成する必要がある場合は、次のコマンドを使用し、セキュリティーリストでポートを開かずにポート5901でsshトンネルを作成します。
    
10.  端末ウィンドウから次のコマンドを使用して、ラップトップ/デスクトップからトンネルを作成します。ここで、ポート5901の通信はsshポート22で行われ、IPアドレス150.136.41.3は要塞サーバーのパブリックIPアドレスです。
    
        $ <copy>ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3</copy>
        
11.  前述のsshトンネル端末ウィンドウは閉じないでください。次に、VNCセッションを開始し、今回はIPアドレスのかわりにポート5901で"localhost"を使用します(このポートがサブネットのセキュリティ・リストでオープンされていない場合でも)。
    
    ![](./images/28-ssh_Tunnel.png " ")
    
12.  要塞サーバー内からParaviewアプリケーションを起動します
    
        $ <copy>cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        $ ./paraview</copy>
        
13.  Paraviewアプリケーション・ウィンドウで、「ファイル」->「開く」->「パス」/mnt/gluster-share/workを選択し、ファイル名motorbike.foamを選択します。ゼロバイト・ファイルになるので問題ありません。
    
    ![](./images/25-Paraview_Menu.png " ")
    
14.  ウィンドウの左の「プロパティ」タブで、「メッシュ・リージョン」を選択してすべての値を選択し、motorBike\_接頭辞で始まらない最上位の値の選択を解除します。motorBike\_で始まるすべての値が選択されていることを確認します。「適用」ボタンをクリックすると、いくつかのエラーがポップアップし、ポップアップ表示されるエラー・ウィンドウを無視してVNCコンソールでイメージのレンダリングを表示します。
    
    ![](./images/26-Mesh_Regions.png " ")
    
15.  次のようなイメージが画面にレンダリングされます。表示設定によっては、画面上のイメージが少し異なる場合があります。
    
    ![](./images/27-Image_Rendering.png " ")
    

すべて完了!これで、OCI上のStandard VMでOpenFoamアプリケーションを実行するためのデモが完了しました。

_完了しました。ワークショップを正常に完了しました_

これらは、High Performance Computeインスタンスの管理に関する詳細情報です。完全なコマンド・リファレンスについては、[ここ](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc)でOCIのドキュメントを確認してください。

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **最終更新者/日付** - Harrison Dvoor、2021年1月