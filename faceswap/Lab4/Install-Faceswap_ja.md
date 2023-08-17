# Faceswapのインストール

## 概要

この演習では、Faceswapをコンピュート・インスタンスにインストールします。Faceswapのドキュメントに従って、[Faceswapのインストール](https://forum.faceswap.dev/viewtopic.php?f=4&t=68)を行います。

予想ワークショップ時間: 30分

**ノート:** GPUインスタンスを使用している場合は、Tensorflow GPUサポートに必要なドライバおよびライブラリの品揃えを**ステップ1**でインストールできます。必要なバージョンのTensorflowには、正しいバージョンのCUDA/cuDNNをインストールする必要があります。それぞれのTensorFlowリリースで使用するCUDAおよびcuDNNバージョンを確認するには、[テストされたビルド構成](https://www.tensorflow.org/install/source#linux)を参照してください。tensorflow-2.4.0の場合、CUDA 11.0をインストールする必要があります。

### 目的

*   オプション: Tensorflow GPUサポートを確保するためのGPUインスタンスの準備
*   FaceswapをUbuntuマシンにインストールします

### 必要なもの

*   完全なラボ1からラボ3まで

## タスク1: CUDA Toolkitのインストール(オプション)

このステップでは、CUDA SDK、GUIツールおよびNvidiaドライバを含むCUDA Toolkitをインストールします。

Faceswapのインストール中に**ステップ2**にインストールされるtensorflow-2.4.0に必要なCUDA Toolkit 11.0 (CUDA 11.0を含む)をインストールします。

**ノート:** Faceswapの将来のリリースでは、どのバージョンのtensorflowがインストールされ、どのバージョンのCUDAがそのバージョンのtensorflowでサポートされているかを必ず確認してください([テスト済ビルド構成](https://www.tensorflow.org/install/source#linux)を参照)。

1.  ターミナルで次のコマンドを入力して、_private-key_をOpenSSH秘密キーに置き換え、_public IP_をUbuntuインスタンスのパブリックIPアドレスに置き換える必要があるUbuntuコンピュート・インスタンスにアクセスします。

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

2.  [CUDA Toolkit 11.0のダウンロードとインストール](https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)のNvidia開発者ドキュメントに詳しく従います

    <copy>
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
    sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
    wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo apt-key add /var/cuda-repo-ubuntu1804-11-0-local/7fa2af80.pub
    sudo apt-get update
    sudo apt-get -y install cuda-11.0
    </copy>
    

3.  次のコマンドを使用して、GPUが表示され、CUDA 11.0が正常にインストールされていることを確認します。

       <copy>nvidia-smi</copy>
    

nvidia-smiの出力は次のようになります。

![](images/nvidia-smi.PNG " ")

## タスク2: Faceswapのインストール

1.  Faceswapインストーラの最新バージョンをダウンロードします。

       <copy>wget https://github.com/deepfakes/faceswap/releases/latest/download/faceswap_setup_x64.sh</copy>
    

2.  ダウンロード場所に移動し、次のコマンドを入力します。

       <copy>bash ./faceswap_setup_x64.sh</copy>
    

[Faceswapインストール・ガイド](https://forum.faceswap.dev/viewtopic.php?f=4&t=68)の構成ステップに従ってください

## **確認**

*   **作成者/日付** - Oracle Proposal to Production Program、 Maria Patelkou氏、HPCソリューション・アーキテクト、2021年3月
*   **最終更新者/日付** - Oracle Proposal to Production Program、 Maria Patelkou氏、HPCソリューション・アーキテクト、2021年3月