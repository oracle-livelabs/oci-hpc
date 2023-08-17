# HPCクラスタ・ネットワークの起動

## 概要

HPCクラスタ・ネットワークを起動する方法は数多くあります。このソリューション・ガイドでは、次の2つの異なる方法について説明します。

*   Marketplace経由
*   OSに応じて手動で、特定の方法を使用します。HPCクラスタ・ネットワーク・マーケットプレイス・イメージまたはOCI HPC CN Terraformスクリプトが使用されている場合、これはOracle Linux 7専用です。CentOS、Ubuntuまたは別のOSを使用する場合は、手動構成が必要です。

推定ラボ時間: 30分

## **オプション1**: マーケットプレイスを介したクラスタ・ネットワークの作成

Marketplaceには、インフラストラクチャとともにデプロイできるアプリケーションとイメージが保持されます。Oracle Linuxを使用するお客様は、HPCクラスタ・ネットワーク・イメージを利用でき、マーケットプレイス内で直接起動できます。CFDに必要なライブラリを含む[CFD対応クラスタ](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211)を起動することをお薦めします。

1.  マーケットプレイス内で、右上の「Get App」を選択します。 ![](images/get-app.png " ")
    
2.  「OCIリージョン」を選択し、「サインイン」をクリックします。 ![](images/sign-in.png " ")
    
3.  サインイン後、HPCクラスタ・イメージのバージョンを確認してから、クラスタが起動されるコンパートメントを選択します。使用条件に同意し、スタックを起動します。
    
    ![](images/launch-stack.png " ")
    
4.  スタックの残りの詳細を入力します。
    
    *   コンピュート・シェイプおよび要塞に必要なADを選択します。
    *   公開SSHキーのコピー・ペースト
    *   クラスタのコンピュート・インスタンス数を入力します
    *   「Install OpenFOAM」の選択を解除します。
    *   6TBを超える共有ディスク領域が必要な場合は、GlusterFSを確認し、必要なサーバー数を選択します(サーバーごとに6TB)。
5.  「Create」をクリックします。
    
6.  「Terraform Actions」に移動し、「Apply」をクリックします。これにより、CNプロビジョニングが起動します。
    
    ![](images/apply.png " ")
    
7.  ジョブが「成功」と表示されるまで待機してから、「出力」に移動して要塞および計算ノードのプライベートIPを取得します。
    

## **オプション2**: 手動構成によるクラスタネットワークの作成

Marketplaceには、インフラストラクチャとともにデプロイできるアプリケーションとイメージが保持されます。Oracle Linuxを使用するお客様の場合は、次のようにクラスタ・ネットワークを手動で作成できます。

1.  右上のOCIリージョンを選択します。
    
2.  メイン・メニューで、「Networking and Virtual Cloud Networks」を選択します。
    
3.  「Start VCN Wizard」をクリックし、「VCN with Internet Connectivity」を選択します。
    
4.  172.16.0.0/16をVCN CIDR、172.16.0.0/24をパブリック・サブネット、172.16.1.0/24をプライベート・サブネットとして選択して名前を付けます。**「次」**、**「作成」**の順にクリックします。
    
5.  メイン・メニューで、「Compute」→「Instances」→「Create Instance」を選択します。
    
6.  **「イメージの変更」**を選択し、「Oracle Image」タブで「Oracle Linux 7 - HPCクラスタ・ネットワーク・イメージ」を選択します。
    
7.  BM.HPC2.36インスタンスを起動できる可用性ドメインを選択します
    
8.  ベア・メタルおよび専門分野の下のシェイプをBM.HPC2.36に変更します
    
9.  作成したVCNおよびパブリック・サブネットを選択します。
    
10.  インスタンスに接続するための公開キーを追加します。このキーは、すべてのコンピュート・インスタンスで使用されます。
    
11.  マシンが起動したら、作成したインスタンスをクリックします。「その他のアクション」で、「インスタンス構成の作成」を選択します。「その他のアクション」でインスタンスを終了できるようになりました。
    
12.  メイン・メニューで、「Compute」→「Cluster Networks」を選択します。
    
13.  「クラスタ・ネットワークの作成」をクリックし、すべてのオプションを入力します。作成したVCN、プライベート・サブネットおよびインスタンス構成を使用します。BM.HPC2.36インスタンスを起動できるADを選択します。
    
14.  クラスタ・ネットワークを起動します。
    
15.  ロード中に、「メイン・メニュー」、「コンピュートおよびインスタンス」の下に別のインスタンスを作成します。
    
16.  公開キーとシェイプを使用して、作成したばかりのパブリック・サブネットにVM.Standard2.1などを配置します。これは、クラスタへの接続に使用する要塞になります。
    
17.  /home/opc/.ssh/cluster\_keyの要塞のクラスタにキーをSCPし、/home/opc/.ssh/id\_rsaにもコピーします
    
18.  次のコマンドを使用して、要塞にプロビジョニング・ツールをインストールします:
    
        sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/n/hpc/b/rpms/o/oci-hpc-provision-20190905-63.7.2.x86_64.rpm
        
19.  コンソールで「Compute」→「Instance Pools」に移動し、クラスタ・ネットワーク・プールのすべてのIPアドレスを収集します。または、プライベート・サブネットで他に何も実行していない場合は、要塞でこのコマンドを使用します。
    
        for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1} >> /tmp/ips; done
        
20.  次のコマンドを使用してプロビジョニング・ツールをインストールします。
    
        ips=`cat /tmp/ips`
        /opt/oci-hpc/setup-tools/cluster-provision/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips
        

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **最終更新者/日付** - Samrat Khosla、2020年10月