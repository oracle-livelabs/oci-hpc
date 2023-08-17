# 安裝 Faceswap

## 簡介

在此實驗室中，我們將在運算執行處理上安裝 Faceswap。我們會仔細閱讀 Faceswap 文件來[安裝 Faceswap](https://forum.faceswap.dev/viewtopic.php?f=4&t=68) 。

預估車間時間：30 分鐘

**注意：**如果您使用 GPU 執行處理，可以在**步驟 1** 中安裝 Tensorflow GPU 支援所需的驅動程式和程式庫組合。需要為所需的 Tensorflow 版本安裝正確的 Cuda/cuDNN 版本。請參閱[測試的組建組態](https://www.tensorflow.org/install/source#linux)，瞭解要搭配個別 TensorFlow 版本使用的 CUDA 和 cuDNN 版本。對於 tensorflow-2.4.0，我們需要安裝 CUDA 11.0。

### 目標

*   選擇性：準備 GPU 執行處理以確保 Tensorflow GPU 支援
*   在我們的 Ubuntu 機器上安裝 Faceswap

### 您需要什麼？

*   完成實驗室 1 至實驗室 3

## 作業 1：安裝 CUDA 工具程式 (選擇性)

在此步驟中，我們將安裝 CUDA 工具程式，包括 CUDA SDK、GUI 工具及 Nvidia 驅動程式。

我們將在 Faceswap 安裝期間安裝 tensorflow-2.4.0 所需的 CUDA Toolkit 11.0 (包括 CUDA 11.0)。**步驟 2**

**注意：**對於未來的 Faceswap 版本，請務必檢查要安裝哪個版本的 tensorflow，以及該版本的 tensorflow 支援哪個 CUDA 版本 (請參閱[測試的組建組態](https://www.tensorflow.org/install/source#linux) )

1.  在您的終端機中輸入下列命令即可存取您的 Ubuntu 運算執行處理，您必須將 _private-key_ 取代為您的 OpenSSH 私密金鑰和 _public ip_ 取代為 Ubuntu 執行處理的公用 IP 位址。

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

2.  我們將密切關注 Nvidia 開發人員文件[下載和安裝 CUDA Toolkit 11.0](https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal) 。

    <copy>
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
    sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
    wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo apt-key add /var/cuda-repo-ubuntu1804-11-0-local/7fa2af80.pub
    sudo apt-get update
    sudo apt-get -y install cuda-11.0
    </copy>
    

3.  檢查 GPU 是否可見，並使用下列指令成功安裝 CUDA 11.0：

       <copy>nvidia-smi</copy>
    

nvidia-smi 輸出如下：

![](images/nvidia-smi.PNG " ")

## 作業 2：安裝 Faceswap

1.  下載最新版的 Faceswap 安裝程式。

       <copy>wget https://github.com/deepfakes/faceswap/releases/latest/download/faceswap_setup_x64.sh</copy>
    

2.  瀏覽至下載位置並輸入下列命令：

       <copy>bash ./faceswap_setup_x64.sh</copy>
    

請依照 [Faceswap Install Guide](https://forum.faceswap.dev/viewtopic.php?f=4&t=68) 中的組態步驟進行

## **確認**

*   **建立者 / 日期** - Maria Patelkou，HPC Solution Architect，Oracle Proposal to Production Program，2021 年 3 月
*   **上次更新者 / 日期** - Maria Patelkou，HPC Solution Architect，Oracle Proposal to Production Program，2021 年 3 月