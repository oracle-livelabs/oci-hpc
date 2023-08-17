# 在 OCI 上在 HPC 集群和标准 VM 上运行 OpenFOAM 项目

## 简介

此运行手册旨在帮助评估 Oracle Cloud Infrastructure 中的 OpenFOAM CFD 软件。它会自动下载并配置 OpenFOAM。OpenFOAM 是免费的开源 CFD 软件，主要由 OpenCFD Ltd 自 2004 年起发布和开发。它拥有来自商业和学术组织的大多数工程和科学领域的用户群。OpenFOAM 提供广泛的功能来解决各种问题，包括化学反应、动荡和热转移、声学、固体力学和电磁学等复杂的流体流动。

### 关于高性能计算 (High Performance Computing，HPC)

HPC，或超级计算，就像日常计算，只有更强大。它是一种以非常高速的速度处理大量数据的方式，使用多台计算机和存储设备作为统一结构。HPC 能够探索和找到一些世界上最大的科学、工程和商业问题的答案。

集群网络是高性能计算 (High Performance Computing，HPC) 实例池，它们通过高带宽、超低延迟的网络连接在一起。集群中的每个节点都是位于与其他节点附近的物理裸金属机。节点之间的远程直接内存访问 (Remote Direct Memory Access，RDMA) 网络可提供与内部部署 HPC 集群相当的低延迟（以单位微秒为单位）。

在 OCI 区域内的 Oracle Cloud Infrastructure 上提供高性能计算。

OCI 中的 Oracle 市场映像和 BM.HPC2.36 中提供了高性能计算实例。

Oracle 市场映像中的高性能计算机架包括 HPC 集群节点、集群网络和 NFS 共享。

计算节点通过集群网络连接，通过该网络为计算节点提供基于 RDMA 的存储访问。

目前，每个计算节点支持一个 BM。它允许客户在保护硬件和网络的同时进行 root 访问，计算节点使用 BM.HPC2.36 进行虚拟化。

_估计时间_：2 小时

### 目标

*   使用通过远程直接内存访问 (Remote Direct Memory Access，RDMA) 互连的 HPC 实例运行 OpenFOAM 项目
    
*   在 Oracle Cloud Infrastructure (OCI) 上使用常规标准虚拟机 (VM) 运行 OpenFOAM 项目
    

### 前提条件

*   具有创建区间和堆栈的权限的 Oracle Cloud Infrastructure 账户。
    
*   熟悉 OCI 控制台和 Oracle Cloud 资源（即虚拟云网络、计算和存储）非常有用。
    
*   假设您已完成 OCI 云架构师专员考试。
    

## 关于此研讨会

*   使用 HPC 集群运行 OpenFOAM 项目
    
*   在 OCI 上使用标准 VM 运行 OpenFOAM 项目
    

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者/日期** - Harrison Dvoor，2021 年 1 月