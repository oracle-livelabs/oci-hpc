# 安装 STAR-CCM+

## 简介

在本练习中，您将安装 STAR-CCM+。

估计实验室时间：25 分钟

## 任务 1：添加特定库

_**如果您从市场使用 CFD 就绪集群，则不需要此步骤**_

1.  有几个库需要添加到所有计算节点上的 Oracle Linux 映像。
    
            sudo yum -y install libSM libX11 libXext libXt
        

## 任务 2：下载二进制文件

您可以从 Siemens PLM 网站下载 STAR-CCM+ 安装程序，也可以使用 scp 将其推送到您的计算机。

        scp /path/own/machine/STAR-CCM_version.zip opc@1.1.1.1:/home/opc/
    

另一种可能性是将安装程序上载到对象存储。

1.  在控制台的主菜单中，选择“Object Storage（对象存储）”。
2.  选择右上角的正确区域
3.  选择左侧的正确区间
4.  创建存储桶（如果尚未创建）
5.  在存储桶中，选择上载对象并指定安装程序的路径。
6.  选择安装程序对象右侧的 3 个点，然后选择 "Create Pre-Authenticated Request"（创建预先验证的请求）
7.  如果您丢失了 URL，则无法返回它，但可以重新生成新的预先验证的请求

下载安装程序表单对象存储，使用

        wget PAR_URL
    

根据您的版本解压缩或解压缩安装程序

        tar -xf installer.tgz
        unzip installer.tgz
    

## 任务 3：安装

1.  在共享位置上启动安装程序。默认情况下，HPC 集群在所有计算节点上都挂载了 NFS 共享。
    
            mkdir /mnt/nfs-share/install
            /path/installscript.sh -i silent -DINSTALLDIR=/mnt/nfs-share/install/
        

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月