# 安装 LS-DYNA

## 简介

在本练习中，您将安装 LS-DYNA。

估计实验室时间：25 分钟

## 任务 1：下载二进制文件

您可以从 [LSTC 网站](http://www.lstc.com/download/ls-dyna)下载 LS-DYNA 二进制文件，或使用 scp 将其推送到您的计算机。

采用为 mpi 创建并针对 RedHat Ent Srv 5.4 编译的版本。根据我们的调查结果，IntelMPI 在 OCI 上的运行速度比平台 MPI 快。（ls-dyna\_mpp\_s\_r10\_1\_123355\_x64\_centos65\_ifort160\_avx2\_intelmpi-413 (1)。tar.gz）

    <copy>
        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    </copy>
    
    

另一种可能性是将安装程序上载到对象存储。

1.  在控制台的主菜单中，选择“Object Storage（对象存储）”。
2.  选择右上角的正确区域
3.  选择左侧的正确区间
4.  创建存储桶（如果尚未创建）
5.  在存储桶中，选择上载对象并指定安装程序的路径。
6.  选择安装程序对象右侧的 3 个点，然后选择 "Create Pre-Authenticated Request"（创建预先验证的请求）
7.  如果您丢失了 URL，则无法返回它，但可以重新生成新的预先验证的请求

下载安装程序表单对象存储，使用

    <copy>
        wget PAR_URL
    </copy>
    

根据您的版本解压缩或解压缩安装程序

    <copy>
    tar -xf installer.tgz
    unzip installer.tgz
    </copy>
    

## 任务 2：安装 LS-DYNA

1.  使二进制文件位于共享位置。默认情况下，HPC 集群在所有计算节点上都挂载了 NFS 共享或 Gluster 共享。
    
        <copy>
        mkdir /mnt/nfs-share/install/lsdyna
        mv ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz /mnt/nfs-share/install/lsdyna/
        cd /mnt/nfs-share/install/lsdyna/
        tar -xf ls-dyna_mpp_s_r10_1_123355_x64_centos65_ifort160_avx2_intelmpi-413.tar.gz
        </copy>
        
        

## 任务 3：安装 MPI 库

**2018 年 Intel MPI**

在每个节点上运行这些命令。

    <copy>
    wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo rpm --import GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
    sudo yum-config-manager --add-repo=https://yum.repos.intel.com/mpi
    sudo yum install -y intel-mpi-2018.4-057 intel-mpi-samples-2018.4-274
    </copy>
    

**平台 MPI**

安装这些库：

    <copy>
    sudo yum install -y glibc.i686 libgcc.x86_64 libgcc.i686
    </copy>
    

从 IBM 网站下载 tar 文件并运行：

    <copy>
    chmod 777 platform_mpi-09.01.04.03r-ce.bin
    ./platform_mpi-09.01.04.03r-ce.bin
    </copy>
    

然后按照屏幕上的说明操作。如果在共享驱动器上安装平台，所有计算节点都可访问该平台。

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月