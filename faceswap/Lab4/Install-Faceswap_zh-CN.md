# 安装 Faceswap

## 简介

在本练习中，我们将在计算实例上安装 Faceswap。我们将密切关注 Faceswap 文档以[安装 Faceswap](https://forum.faceswap.dev/viewtopic.php?f=4&t=68) 。

估计研讨会时间：30 分钟

**注：**如果使用的是 GPU 实例，则可以在**第 1 步**中安装 Tensorflow GPU 支持所需的各种驱动程序和库。需要为所需的 Tensorflow 版本安装正确的 Cuda/cuDNN 版本。要了解要与各自的 TensorFlow 发行版一起使用的 CUDA 和 cuDNN 版本，请参阅[测试的构建配置](https://www.tensorflow.org/install/source#linux)。对于 tensorflow-2.4.0，我们需要安装 CUDA 11.0。

### 目标

*   可选：准备 GPU 实例以确保 Tensorflow GPU 支持
*   在我们的 Ubuntu 计算机上安装 Faceswap

### 你需要什么？

*   完成实验室 1 到实验室 3

## 任务 1：安装 CUDA 工具包（可选）

在此步骤中，我们将安装 CUDA 工具包，其中包括 CUDA SDK、GUI 工具和 Nvidia 驱动程序。

我们将在安装 Faceswap 的**步骤 2** 中安装 tensorflow-2.4.0 所需的 CUDA 工具包 11.0（包括 CUDA 11.0）。

**注：**对于将来的 Faceswap 发行版，请始终确保检查要安装的 tensorflow 版本以及该版本的 tensorflow 支持的 CUDA 版本（请参见[测试的构建配置](https://www.tensorflow.org/install/source#linux)）

1.  在您的终端中输入以下命令来访问 Ubuntu 计算实例，在该实例中，您需要使用 OpenSSH 私钥替换 _private-key_ ，使用 Ubuntu 实例的公共 IP 地址替换 _public ip_ 。

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

2.  我们将密切关注 Nvidia 开发人员文档，了解[下载和安装 CUDA Toolkit 11.0](https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Linux&target_arch=x86_64&target_distro=Ubuntu&target_version=1804&target_type=deblocal)

    <copy>
    wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
    sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
    wget http://developer.download.nvidia.com/compute/cuda/11.0.2/local_installers/cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo dpkg -i cuda-repo-ubuntu1804-11-0-local_11.0.2-450.51.05-1_amd64.deb
    sudo apt-key add /var/cuda-repo-ubuntu1804-11-0-local/7fa2af80.pub
    sudo apt-get update
    sudo apt-get -y install cuda-11.0
    </copy>
    

3.  使用以下命令检查 GPU 是否可见以及 CUDA 11.0 是否已成功安装：

       <copy>nvidia-smi</copy>
    

nvidia-smi 输出如下所示：

![](images/nvidia-smi.PNG " ")

## 任务 2：安装 Faceswap

1.  下载 Faceswap 安装程序的最新版本。

       <copy>wget https://github.com/deepfakes/faceswap/releases/latest/download/faceswap_setup_x64.sh</copy>
    

2.  导航到下载位置并输入以下命令：

       <copy>bash ./faceswap_setup_x64.sh</copy>
    

请按照 [Faceswap 安装指南](https://forum.faceswap.dev/viewtopic.php?f=4&t=68)中的配置步骤操作

## **确认**

*   **创建者/日期** - Oracle 建议到生产计划 HPC 解决方案架构师 Maria Patelkou，2021 年 3 月
*   **上次更新者/日期** - Oracle 建议到生产计划 HPC 解决方案架构师 Maria Patelkou，2021 年 3 月