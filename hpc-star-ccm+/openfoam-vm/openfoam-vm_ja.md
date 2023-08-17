# OpenFoamプロジェクトをOCI上の標準VMで実行します

## 概要

このラボは、Oracle Cloud InfrastructureでのOpenFOAM CFDソフトウェアの評価を支援するように設計されています。OpenFOAMが自動的にダウンロードおよび構成されます。OpenFOAMは、2004年以降主にOpenCFD Ltdがリリースおよび開発した無料のオープン・ソースCFDソフトウェアです。商業組織と学術組織の両方から、エンジニアリングおよび科学のほとんどの領域に大規模なユーザー基盤があります。OpenFOAMには、化学反応、乱流および熱伝達を含む複雑な流体フローから音響学、固体機械学、電磁学に至るまで、様々な機能があります。

この演習では、基本的なVM.Standard 2.1シェイプでOpenFoamをインストールする方法について説明しますが、実際のデプロイメントでは、BM.HPC2.36のような大きいシェイプが適切です。高パフォーマンス・シェイプの中には、特定のリージョンとアベイラビリティ・ドメインでのみ使用可能なものがあります。

この演習では、パブリック・サブネット上のクラスタのヘッドノードとプライベート・サブネット内のワーカー・コンピュート・ノードの2つのノードを作成します。ワーカー・ノードにアクセスするには、まずヘッドノードを作成し、ヘッドノードにSSHキーを生成し、ワーカー・ノードの作成時にその公開キーを使用します。

**問題を記録**するには、[ここ](https://github.com/oracle/learning-library/issues/new)をクリックして、github oracle repository issue submitフォームに移動します。

推定ラボ時間: 60分

### 目的

開発者のデータエンジニアとして、

1.  High Performance Computingインスタンスを手動でデプロイ
2.  クラスタ・ネットワークの作成
3.  ネットワーク・ファイル・システムの作成
4.  VNCの設定
5.  OpenFoamおよびParaviewのインストール

### 前提条件

1.  インスタンスのVM標準2.1またはBM.HPC2.36シェイプを作成する権限を持つOracle Cloud Infrastructureアカウント。

### インフラストラクチャの用語

*   ワーカー・ノード: 計算シミュレーションまたはその他のエンジニアリング・ワークロードのワークロードを実行するための処理能力を提供するHPCコンピュート・インスタンス。このデモ・コンピュート・シェイプでは、BM.HPC2.36ノードがワーカー・ノードです。
*   ヘッド・ノード: すべての計算ジョブをスケジュールしてワーカー・ノードで実行するために発行できるコンピュート・インスタンス。HeadノードとBastionノードは、同じマシンにすることができます。このデモでは、要塞ノードをプロビジョニングしています。
*   要塞ノード: パブリックIPを持つコンピュート・インスタンスは、通常プライベート・ネットワークにあるワーカー・ノードに接続するエントリ・ポイントとして機能します。
*   RoCEネットワーク: RDMA over Converged Ethernet (RoCE)は、Ethernetネットワークを介したリモート・ダイレクト・メモリー・アクセス(RDMA)を可能にするネットワーク・プロトコルです。

## タスク1: Oracle Virtual Cloud Networkのプロビジョニング

1.  インスタンスを作成する前に、Virtual Cloudネットワークを構成する必要があります。左上のメニューを選択し、「Networking and Virtual Cloud Networks ![](./images/vcn.png) ![](./images/create_vcn.png)」を選択します。 ![](./images/vcn_content.png)
    
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
5.  **「サブネットの作成」** ![](./images/create_subnet.png)をクリックします ![](./images/create_subnet_content.png)
    
6.  インターネット・ゲートウェイの作成
    
    1.  作成した`hpc_vcn`をクリックし、ページの左側にある**「リソース」**メニューで**「インターネット・ゲートウェイ」**を選択して、インターネット・ゲートウェイを作成します。![](./images/create_IG.png) ![](./images/create_IG_content.png)

ノート: これにより、インターネット・ゲートウェイが作成され、ルート表に関連付ける必要があります。この場合、デフォルト・ルート表はパブリック・サブネットに使用されるため、インターネット・ゲートウェイはそのルート表に関連付ける必要があります。

7.  ルート表にルート・ルールを追加します。ページの左側にある**「リソース」**メニューで、**「`hpc_vcn`のデフォルト・ルート表」**を選択し、**「ルート・ルールの追加」**をクリックします
    *   ターゲット・タイプ: Internet Gateway
    *   宛先CIDRブロック: 0.0.0.0/0
    *   コンパートメント内のターゲット・インターネット・ゲートウェイ: ![](./images/route_table.png)を作成したインターネット・ゲートウェイ ![](./images/route_rules.png)

## タスク2: クラスタ・ノードの作成

この演習では、パブリック・サブネット上のクラスタの**Headnode**とプライベート・サブネットの**ワーカー・コンピュート・ノード**の2つのノードを作成します。ワーカー・ノードにアクセスするには、まずヘッドノードを作成し、ヘッドノードにSSHキーを生成し、ワーカー・ノードの作成時にその公開キーを使用します。

注意: この演習では、基本的なVM.Standard2.1シェイプのみを使用しますが、実際のデプロイメントではBM.HPC2.36のような大きいシェイプが適しています。高パフォーマンス・シェイプの中には、特定のリージョンとアベイラビリティ・ドメインでのみ使用可能なものがあります。

1.  #### ヘッドノードの作成
    
    *   名前: hpc\_head
    *   イメージまたはオペレーティング・システム: 最新バージョンのOracle Linux(デフォルト)
    *   Availability Domain: VM.Standard 2.1シェイプで使用可能なドメイン
    *   インスタンスのシェイプ:
        *   VM.Standard 2.1
        *   その他のシェイプ(使用可能な場合はBM.HPC2.36を優先)
    *   Virtual Cloud Network: 以前に作成されたVCN
    *   Subnet: 前に作成されたパブリック・サブネット
    *   SSH鍵: 公開鍵ファイルを添付します。
    
    1.  左上のメニューで、「コンピュート」を選択してインスタンスを作成します。![](./images/compute.png) ![](./images/compute_bm.png) ![](./images/compute_vm.png)
        
    2.  ヘッドノードにSSH接続し、クラスタが通信するためのSSHキーを生成します。
        
            ```
            $ ssh -i <ssh_key> opc@<headnode_ip_address>
            
            $ ssh-keygen
            ```
            Note: Do not change the ssh key file location (/home/opc/.ssh/id_rsa) and hit enter when asked about a passphrase (twice).
            
            1. Run and Copy the whole string, which will be used in creating the worker node
            
            ```
            $ cat ~/.ssh/id_rsa.pub
            ```
            
2.  **ワーカー・ノードの作成**
    
    1.  左上のメニューで「Compute」を選択し、次の詳細を含むインスタンスを作成します:
        
        *   名前: hpc\_worker1
        *   イメージまたはオペレーティング・システム: 最新バージョンのOracle Linux(デフォルト)
        *   Availability Domain: VM.Standard 2.1シェイプで使用可能なドメイン
        *   インスタンスのシェイプ:
            *   VM.Standard 2.1
            *   その他のシェイプ(使用可能な場合はBM.HPC2.36を優先)
        *   Virtual Cloud Network: 以前に作成されたVCN
        *   Subnet: 前に作成されたプライベート・サブネット
        *   SSHキー: 上の1つのステップからコピーされた公開キー文字列。
    2.  ワーカー・ノードにSSH接続します。  
        ヘッドノードにログインしたコンソールに戻り、プライベートIPアドレスを取得して、ヘッドノードからワーカーノードにSSHを実行します
        
            ```
            $ ssh opc@10.x.x.x
            ```
            

## タスク3: NAT Gatewayの設定

\*\*これはワーカー・ノード専用です\*\*  

1.  ワーカー・ノードを選択し、左側の**「リソース」**メニューで**「アタッチされたVNIC」**をクリックします
2.  **「VNICの編集」**を選択します
3.  **「ソース/宛先チェックのスキップ」**を選択解除し、**「VNICの更新」**をクリックします ![](./images/nat_gateway.png)

## タスク4: ドライブのマウント

ノート: ノード・シェイプにNVMeがアタッチされている(BM.HPC2.36にVM.Standard2.1ではなく、BM.HPC2.36がアタッチされている)場合にのみ、HPCマシンにはローカルのNVMeストレージがありますが、デフォルトではマウントされていません。VM.Standard2.1を使用している場合はステップ5にスキップします

1.  ヘッドノードへのSSHを実行し、次のコマンドを実行します

    ```
     $ lsblk
    ```
    The drive should be listed with the NAME on the left (Probably nvme0n1, if it is different, change it in the next commands)
    

ヘッドノードには、インストールおよびモデルとの共有ドライブが含まれます。これは、異なるすべてのワーカー・ノード間で共有されます。各ワーカー・ノードは、NVMeドライブでローカルに実行されているドライブもマウントします。この例では、共有ディレクトリは500 GBですが、自由に変更できます。

2.  ワーカー・ノードでドライブをパーティション化します(オプション)。

    ```
     $ sudo yum -y install gdisk
    ```
    ```
     $ sudo gdisk /dev/nvme0n1
     $ > n    # Create new partition
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
    ```
    

3.  ワーカー・ノードのドライブのフォーマット

    ```
    $ sudo mkfs -t ext4 /dev/nvme0n1
    ```
    

4.  ヘッドノード`sudo mkfs -t ext4 /dev/nvme0n1p1 sudo mkfs -t ext4 /dev/nvme0n1p2`のパーティションのフォーマット
    
5.  ディレクトリを作成し、ドライブをマウントします
    
6.  Headnode(localおよびshare): 500Gパーティションのマウント・ディレクトリとして/mnt/shareを選択し、大きいパーティションの場合は/mnt/localを選択します。
    
         sudo mkdir /mnt/local
         sudo mount /dev/nvme0n1p1 /mnt/share
         sudo chmod 777 /mnt/share
         sudo mount /dev/nvme0n1p2 /mnt/local
         sudo chmod 777 /mnt/local
        
        
7.  ヘッドノード(共有):
    
         sudo mkdir /mnt/share
         sudo mount /dev/nvme0n1 /mnt/share
         sudo chmod 777 /mnt/share
        
8.  ワーカー・ノード: ドライブ全体のマウント・ディレクトリとして/mnt/localを選択します。
    
         sudo mkdir /mnt/local
         sudo mount /dev/nvme0n1 /mnt/local
         sudo chmod 777 /mnt/local
        

## タスク5: ネットワーク・ファイル・システムの作成

1.  マウント・ターゲットを作成します。左上のメニューで、**「File Storage」**および**「Mount Target」**をクリックします。

    - New Mount Target Name: Enter a name (example: openfoam_fs)
    - Virtual Cloud Network: Select the VCN created above
    - Subnet: Select the public VCN
    

2.  Headnodeはパブリック・サブネット内にあります。ファイアウォールを稼働させ、次の方法で例外を追加します。  
    

    ```
    sudo firewall-cmd --permanent --zone=public --add-service=nfs
    sudo firewall-cmd --reload
    ```
    

3.  作成されたマウント・ターゲットをクリックし、エクスポート・パスをクリックして、マウント・コマンドをクリックし、次のようなコマンドをコピーします: `sudo yum install nfs-utils sudo mkdir -p /mnt/share sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share`
    
4.  ワーカー・ノードでは、アクセスを制限するセキュリティ・リストがあるプライベート・サブネットにあるため、ファイアウォールを完全に無効にできます。次に、nfs-utilsツールをインストールし、前述のようにnfsをマウントできます。
    

    ```
    sudo systemctl stop firewalld
    sudo yum -y install nfs-utils
    sudo mkdir  -p /mnt/share
    sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share
    ```
    

## タスク6: OpenFOAMのインストール

1.  **すべてのワーカー・ノードの接続**  
    

    Each worker node needs to be able to talk to all the worker nodes. SSH communication works but most applications have issues if all the hosts are not in the known host file. To disable the known host check for nodes with address in the VCN, you can deactivate with the following commands. You may need to modify it slightly if your have different addresses in your subnets. Put the following code block in a shell script and run the script. 
    
        ```
        for i in 0 1 2 3
        do
            echo Host 10.0.$i.* | sudo tee -a ~/.ssh/config
            echo "    StrictHostKeyChecking no" | sudo tee -a ~/.ssh/config
        done
        ```
    

2.  **機械リストの作成**  
    

    OpenFOAM on the headnode does not automatically know which compute nodes are available. You can create a machinefile at `/mnt/share/machinelist.txt with` the private IP address of all the nodes along with the number of CPUs available. The headnode should also be included. The format for the entries is `private-ip-address cpu=number-of-cores`
    
        ```
        10.0.0.2 cpu=1
        10.0.3.2 cpu=1
        ```
    

3.  **Headnode**  
    

    Install from sources, modify the path to the tarballs in the next commands. This example has the foundation OpenFOAM sources. OpenFOAM from ESI has also been tested. To share the installation between the different compute nodes, install on the network file system.
    
    
        ```
        sudo yum groupinstall -y 'Development Tools'
        sudo yum -y install devtoolset-8 gcc-c++ zlib-devel openmpi openmpi-devel
        cd /mnt/share
        wget -O - http://dl.openfoam.org/source/7 | tar xvz
        wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
        mv OpenFOAM-7-version-7 OpenFOAM-7
        mv ThirdParty-7-version-7 ThirdParty-7
        export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | sudo tee -a ~/.bashrc
        echo export $ LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        echo source /mnt/share/OpenFOAM-7/etc/bashrc | sudo tee -a ~/.bashrc
        sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        source ~/.bashrc
        cd /mnt/share/OpenFOAM-7
        ./Allwmake -j
    
        ```
    

4.  **ワーカー・ノード**  
    
        sudo yum -y install openmpi openmpi-devel
        cd /mnt/share
        export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | $ sudo tee -a ~/.bashrc
        echo export $LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        echo source /mnt/share/OpenFOAM-7/etc/bashrc | $ sudo tee -a ~/.bashrc
        sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        source ~/.bashrc
        
        

## タスク7: シミュレーション・ワークロードの実行および出力のレンダリング

1.  Headnodeで、Paraviewパッケージを使用して出力をレンダリングするために必要な次のコマンドを実行します。
    
         ```
         $ sudo yum install -y mesa-libGLU
         $ cd /mnt/share
         $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" $ https://www.paraview.org/paraview-downloads/download.php > file.tar.gz
         $ tar -xf file.tar.gz
         ```
        
2.  headnondeで、次のコマンドを実行してVNCサーバーを設定します:  
    
        $ sudo yum -y groupinstall 'Server with GUI'
        $ sudo yum -y install tigervnc-server mesa-libGL
        $ sudo mkdir /home/opc/.vnc/
        $ sudo chown opc:opc /home/opc/.vnc
        $ echo "HPC_oci1" | vncpasswd -f > /home/opc/.vnc/passwd
        $ chown opc:opc /home/opc/.vnc/passwd
        $ chmod 600 /home/opc/.vnc/passwd
        $ /usr/bin/vncserver
        
3.  ワーカー・ノードの1つで、/mnt/share/workの[スクリプト](../scripts/motorbike_RDMA.tgz)を使用してこのzipをダウンロードします。`tar -xf motorbike_RDMA.tgz`を使用してファイルを解凍します
    
4.  ワークロードを実行する前に、構築したアーキテクチャに適合するようにallrunファイルを編集する必要があります。まず、OpenFOAMインストーラ・フォルダからフォルダを移動します
    
         ```
         $ model_drive=/mnt/share
         $ sudo mkdir $model_drive/work
         $ sudo chmod 777 $model_drive/work
         $ cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
         $ cd /mnt/share/work/motorBike/system
         ```
        
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
    

6.  次に、/mnt/share/work/motorBikeのAllrunファイルを次のように編集します: \`\` #!/bin/sh cd ${0%/\*} || exit 1 #このディレクトリから実行NP=$1 install\_drive=/mnt/share #ソース・チュートリアル実行関数。$WM\_PROJECT\_DIR/bin/tools/RunFunctions
    
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
         touch motorbike.foam
         ```
        
7.  ワーカー・ノードにいることを確認し、ワークロードを実行します: `$ ssh worker_node_IP $ cd /mnt/share/work/ $ ./Allrun 2`
    
         ```
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
         219.95
        
         ```
        
8.  ワークロードが正常に完了したら、次のようにマシンでVNCクライアントを構成します。要塞サーバーおよびVNCポートのパブリックIPの指定 ![](./images/24-VNC_Connection.png)
    
9.  オプション: VNCポート5901を開けない場合、またはセキュリティー上の理由によりこのポートのsshトンネルを作成する必要がある場合は、次のコマンドを使用し、セキュリティーリストでポートを開かずにポート5901でsshトンネルを作成します。
    
10.  端末ウィンドウから次のコマンドを使用して、ラップトップ/デスクトップからトンネルを作成します。ここで、ポート5901の通信はsshポート22で行われ、IPアドレス150.136.41.3は要塞サーバーのパブリックIPアドレスです。
    
        ```
        ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3
        ```
        
11.  前述のsshトンネル端末ウィンドウは閉じないでください。次に、VNCセッションを開始し、今回はIPアドレスのかわりにポート5901で"localhost"を使用します(このポートがサブネットのセキュリティ・リストでオープンされていない場合でも)。 ![](./images/28-ssh_Tunnel.png)
    
12.  要塞サーバー内からParaviewアプリケーションを起動します
    
        ```
        $ cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        $ ./paraview
        ```
        
13.  Paraviewアプリケーション・ウィンドウで、「ファイル」->「開く」->「パス」/mnt/gluster-share/workを選択し、ファイル名motorbike.foamを選択します。ゼロバイト・ファイルになるので問題ありません。 ![](./images/25-Paraview_Menu.png)
    
14.  ウィンドウの左の「プロパティ」タブで、「メッシュ・リージョン」を選択してすべての値を選択し、motorBike\_接頭辞で始まらない最上位の値の選択を解除します。motorBike\_で始まるすべての値が選択されていることを確認します。「適用」ボタンをクリックすると、いくつかのエラーがポップアップし、ポップアップ表示されるエラー・ウィンドウを無視してVNCコンソールでイメージのレンダリングを表示します。 ![](./images/26-Mesh_Regions.png)
    
15.  次のようなイメージが画面にレンダリングされます。表示設定によっては、画面上のイメージが少し異なる場合があります。 ![](./images/27-Image_Rendering.png)
    

すべて完了!これで、OCI上のStandard VMでOpenFoamアプリケーションを実行するためのデモが完了しました。

_完了しました。ワークショップを正常に完了しました_

これらは、High Performance Computeインスタンスの管理に関する詳細情報です。完全なコマンド・リファレンスについては、[ここ](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc)でOCIのドキュメントを確認してください。

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **最終更新者/日付** - Harrison Dvoor、2020年10月

# <<<<<<ヘッド

> > > > > > > アップストリーム/マスター