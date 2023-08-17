# OCI CLIコマンドおよびTerraformを使用したHPCの操作

## 概要

`ocihpc`は、Oracle Cloud Infrastructure (OCI)でのHPCアプリケーションのデプロイメントを簡略化するツールです。HPCコマンドライン・インタフェース(CLI)では、単一のコマンドを使用して、専用のベア・メタルHPCコンピュート・インスタンスとMellanox RDMAネットワーキングを使用して、あらゆるサイズのHPCクラスタをデプロイできます。HPC CLIを使用するためにTerraformまたはOracle Cloud Infrastructure Resource Managerを理解する必要はありません。コアOracle Cloud Infrastructure CLI上に構築され、事前構築済のTerraformスクリプトが含まれています。

HPC CLIを使用する利点は次のとおりです。

1.  単一のコマンドでRDMA over Converged Ethernet (RoCE) v2を使用して、完全なコンピュート・クラスタを迅速に起動します。
2.  使いやすいTerraformまたはOracle Cloud Infrastructure Resource Managerの知識がなく、基本的なクラスタ・ネットワークを起動する必要があります。
3.  Supports Message Passing Interface (MPI) Deploymentには、Open MPI、Intel MPI、Platform MPIを含むMellanox OFEDを含む、RDMAとの並列処理を実行するための完全なソフトウェアパッケージセットが含まれています。
4.  カスタマイズ可能 独自のterraformスクリプトを実行するか、ツールで提供される既存のスクリプトに追加して独自のアプリケーションをインストールできます。

推定ラボ時間: 60分

### 目的

開発者またはデータ・エンジニアとして、

1.  Oracleコマンドライン・インタフェースを使用したHPCクラスタ・ネットワークのデプロイ
2.  Cli経由でスタックを表示、実行および管理

### 前提条件

1.  Terraformスタックを実行するのに十分なユーザー資格証明を備えたOracle Cloud Infrastructureアカウント。
2.  生成済みSSH鍵

### ポリシー

`ocihpc`で使用するOCIユーザー・アカウントには、OCIリソース・マネージャに必要なポリシーが構成されている必要があります。必要なポリシーの詳細は、[このリンク](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingstacksandjobs.htm)を確認してください。

## タスク1: macOS/Linuxへのocihpcのインストール

1.  次のコマンドを使用して最新リリースをダウンロードし、解凍します。
    
        $ curl -LO https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_darwin_x86_64.tar.gz
        
2.  ステップ1でダウンロードしたファイルを解凍します。
    
        $ gunzip -c ocihpc_v1.0.0_darwin_x86_64.tar.gz | tar xopf -
        
3.  ocihpcバイナリを実行可能にします。
    
        $ chmod +x ./ocihpc 
        
4.  ocihpcバイナリをPATHに移動します。
    
        $ sudo mv ./ocihpc /usr/local/bin/ocihpc 
        
5.  機能することをテストします。
    
        $ ocihpc version 
        

## タスク2: Windowsでのocihpcのインストール

1.  最新リリースを[このリンク](https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_windows_x86_64.zip)からダウンロードし、抽出します。
    
2.  ocihpcバイナリをPATHに追加します。
    
3.  機能することをテストします。
    
        $ ocihpc.exe version 
        

## タスク3: コマンドラインでのSSHキーペアの作成

ウィンドウを使用している場合は、[このリンク](https://docs.cloud.oracle.com/en-us/iaas/Content/GSG/Tasks/creatingkeys.htm)を参照してください。

1.  コマンドを入力するためのシェルまたはターミナルを開きます。
    
2.  .ociフォルダに移動します
    
        $ cd Users/enjli/.ssh
        
3.  フォルダをまだ作成していない場合は、資格証明を格納する.sshディレクトリを作成します。
    
        $ mkdir Users/enjli/.ssh
        
4.  フォルダにid\_rsaキーのペアが表示されない場合は、次のコマンドを入力し、プロンプトが表示されたら名前とパスフレーズを指定します。キーは、2048ビットのRSAキーというデフォルト値で作成されます。
    
        $ ssh-keygen
        

## タスク4: API署名キーの生成

APIリクエストは秘密キーで署名され、Oracleは公開キーを使用してリクエストの信頼性を検証します。ウィンドウを使用している場合は、[このリンク](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#Other)を参照してください。

1.  まだ作成していない場合は、資格証明を格納する.ociディレクトリを作成します。
    
        $ mkdir Users/enjli/.oci
        
2.  次のコマンドを使用して秘密キーを生成します。
    
        $ openssl genrsa -out ~/.oci/oci_api_key.pem 2048
        
3.  秘密キー・ファイルは、自分だけが読み取れるようにしてください:
    
        $ chmod go-rwx ~/.oci/oci_api_key.pem
        
4.  公開キーを生成します:
    
        $ openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem
        

## タスク5: Oracle Cloud Infrastructureへの公開キーの追加

プライベート/公開キーのコンボがあるので、ユーザー設定でそれをOCIコンソールに追加する必要があります:

1.  `oci_api_key_public.pem`を含む.ociフォルダに移動し、公開キーをコピーします。
    
2.  OCIコンソールにログインし、「Menu」をクリックして「Identity and Users」を選択します。ユーザーを選択し、「ユーザー詳細」ページにナビゲートします。
    
3.  「API Keys」セクションの「Add Public Key」をクリックします。![](./images/ResourcesMenu.png) ![](./images/APIKeys.png)
    
4.  公開キーの追加でCLIからコピーした公開キーの貼付け ![](./images/AddPublicKey.png)
    

## タスク6: 構成

この手順では、CLIの必須構成について説明します。CLIの機能を拡張するためのオプション構成も含まれます。

1.  CLIを使用する前に、Oracle Cloud Infrastructureアカウントの操作に必要な資格証明を含む構成ファイルを.ociフォルダに作成する必要があります。このファイルは、設定ダイアログを使用して作成することも、テキスト・エディタを使用して手動で作成することもできます。
    
2.  _ocihpc configure_を実行して、OCIにアクセスするための有効な構成があるかどうかを確認します。このツールでは、構成の作成を順を追って説明します。
    

構成ファイルの書込み先が通知されます:

_構成ファイルが保存されました: /Users/enjli/.oci/config_

構成ファイルを作成するには、1が必要です。ユーザーOCID (画面の右上にあるプロファイル・セクション→「ユーザー設定」→「ユーザー情報」タブ) 2。テナンシOCID (「管理」→「テナンシ詳細」→「テナンシ情報」タブ)、3。作業中のリージョン(us-phoenix-1、us-ashburn-1など)、4。ユーザーの指紋(ユーザー情報タブに見つかりました)、5。API秘密署名キーへのパス。

`config`ファイルの例は次のようになります。 ![](./images/oci_config.png)

## タスク7: リスト

`ocihpc list`を実行すると、使用可能なスタックのリストを取得できます。

例:

    ```
    sh
    $ ocihpc list
    
    List of available stacks:
    
    ClusterNetwork
    Gromacs
    OpenFOAM
    ```
    

## タスク8: 初期化

1.  デプロイメント・ソースとして使用するフォルダを作成します。

重要: スタックごとに異なるフォルダを使用してください。同じフォルダ内の複数のスタックを初期化しないでください。それ以外の場合、ツールは前のツールを上書きします。

2.  そのフォルダに変更し、`ocihpc init <stack name>`を実行します。`ocihpc`により、必要なファイルがそのフォルダにダウンロードされます。
    
        $ mkdir ocihpc-test
        $ cd ocihpc-test
        $ ocihpc init --stack ClusterNetwork
        
        Downloading stack: ClusterNetwork
        
        ClusterNetwork downloaded to /Users/enjli/ocihpc-test/
        
        

**重要**: ocihpc deployコマンドを実行する前に、/Users/enjli/ocihpc-test/config.jsonファイルの内容を編集します

## タスク9: デプロイ

1.  デプロイする前に、`config.json`ファイルの値を変更する必要があります。変数は、デプロイするスタックによって異なります。クラスタ・ネットワークの`config.json`の例は、次のようになります。
    
        {
          "variables": {
            "ad": "kWVD:PHX-AD-1",
            "bastion_ad": "kWVD:PHX-AD-2",
            "bastion_shape": "VM.Standard2.1",
            "node_count": "2",
            "ssh_key": "ssh-rsa AAAAB3NzaC1yc2EAAAA......W6 enjli@enjli-mac"
          }
        }
        
2.  `config.json`ファイルを変更するには、新しく作成したディレクトリ(この場合はocihpc-test)に移動し、texteditorまたはメモ帳を使用してconfig.jsonファイルを開きます。 ![](./images/config_json.png)
    

これは、ステップ1で構成した構成ファイルとは異なります。

この構成ファイルには、次のものが必要です:

1.  テナンシのHPCリソースを含む可用性ドメイン情報(「管理」>「テナンシ詳細」>「サービス制限」セクション>「コンピュート」まで下にスクロールして「BM.HPC2.36」) - 次のスクリーンショットでは、現在使用中のAD-2で使用するために合計6台のBM.HPC2.36マシンがあることがわかります。 ![](./images/hpc_resource.png)
    
2.  要塞ADは、リソース(VM.standard2.1シェイプ)があるかぎり、選択したADにできます。要塞シェイプはすでに入力されている必要があります- VM.Standard2.1
    
3.  ノード数- この演習では、すべてのHPCリソースを使用できるように2を使用します。
    
4.  公開SSHキー
    

**ノート**

1.  `config.json`の値を変更した後、`ocihpc deploy <arguments>`を使用してスタックをデプロイできます。このコマンドは、Oracle Cloud Resource Managerにスタックを作成し、それを使用してスタックをデプロイします。
    
2.  サポートされているスタックの場合、デプロイするノードの数を設定するには、`ocihpc deploy`コマンドに追加します。スタックがサポートされていない場合、または値を指定しない場合、ツールはデフォルト番号でデプロイされます。
    
3.  たとえば、次のコマンドは、5つのノードを持つクラスタネットワークを配備します。
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
4.  このツールは、`<stack name>-<current directory>-<random-number>`で構成されるデプロイメント名を生成します。
    
    例:
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 0sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 17sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 35sec]
        ...
        

## タスク10: 接続

デプロイメントが完了すると、接続できる要塞/ヘッドノードIPが表示されます:

    ```
    $ Successfully deployed ClusterNetwork-ocihpc-test-7355
    
    $ You can connect to your head node using the command: ssh opc@$123.221.10.8 -i <location of the private key you used>
    ```
    

`ocihpc get ip`コマンドを実行して接続の詳細を取得することもできます。

## タスク11: 管理

さらに、cliコマンドを使用して、リソースのトラッキングを簡単に管理および保持できます。

1.  特定のコンパートメントにデプロイされたすべてのスタックのリストを生成するには: `oci resource-manager job list -c [OCID OF COMPARTMENT]`
    
2.  スタックまたはコンパートメント内のジョブのリストを生成するには、作成された時間順にソートします: `oci resource-manager job list -c [COMPARTMENT OCID]`
    
3.  ジョブとジョブの詳細を確認するには: `oci resource-manager job get --job-id [OCID OF THE JOB]`
    
4.  指定したジョブのログ・エントリをJSON形式で表示するには: `oci resource-manager job get-job-logs --job-id [OCID OF THE JOB]`
    
5.  スタックとその関連ジョブを別のコンパートメントに移動するには: `oci resource-manager stack change-compartment -c [OCID OF NEW COMPARTMENT] --stack-id [OCID OF THE STACK]`
    

## タスク12: 削除

デプロイメントが完了したら、スタック・フォルダに変更して`ocihpc delete --stack <stack name>`を実行すると、デプロイメントを削除できます。

    Example:
    ```
    $ ocihpc delete --stack ClusterNetwork
    
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 0sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 17sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 35sec]
    ...
    
    Successfully deleted ClusterNetwork-ocihpc-test-7355
    ```
    

すべて完了!OCI CLIツールを使用して、最初のHigh Performance Computeインスタンスを正常にデプロイしました。

_次の演習に進むことができます。_

これは、High Performance Computeインスタンスの管理に関する詳細情報です。完全なコマンド・リファレンスについては、[ここ](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc)でOCIのドキュメントを確認してください。

## 確認

*   **作成者** - High Performance Computeチーム
*   **貢献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **最終更新者/日付** - Harrison Dvoor、2020年10月

# <<<<<<ヘッド

> > > > > > > アップストリーム/マスター