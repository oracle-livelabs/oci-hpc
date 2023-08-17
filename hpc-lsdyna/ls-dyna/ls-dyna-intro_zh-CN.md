# LS\_DYNA 简介

LS-DYNA 是一个通用有限元素程序，能够模拟复杂的现实世界问题。它被汽车、航空航天、建筑、军事、制造和生物工程行业使用。 [LS-DYNA 网站](https://www.lstc.com/products/ls-dyna)

此 Runbook 将带您完成在 Oracle Cloud 上部署 LS Dyna 集群的过程，在计算节点之间建立低延迟网络。在 Oracle Cloud 上运行 LS Dyna 非常简单，请按照本指南中介绍所有技巧和技巧。

![](images/3cars.jpg)

## **体系结构**

此运行手册的体系结构如下所示，我们将连接一个小型计算机（堡垒）。计算节点将位于与 RDMA RoCE v2 网络关联的独立专用网络上。堡垒可以通过 SSH 从具有密钥的任何人（如果您决定启用它，则 VNC）进行访问。计算节点只能通过网络中的堡垒进行访问。这可以通过包含 2 个子网的 1 个虚拟云网络、一个公共和一个专用云网络实现。

### 基线基础结构

以下区域支持群集网络。在每种情况下，我们建议使用基准参考架构，然后根据需要对其进行调整以满足您的具体要求：

*   VCN
    
    *   公共子网、安全列表、路由表
    *   专用子网、安全列表、路由表
    *   Internet 网关
    *   NAT 网关
*   计算节点
    
*   公共子网中的堡垒主机
    
*   专用子网中的 HPC 计算节点
    

![](images/images.png)

上面的基线基础结构提供了以下规范：

*   网络
    *   1 x 100 Gbps RDMA over Converged ethernet (ROCE) v2
    *   延迟低至 1.5 微秒
*   HPC 计算节点 (BM.HPC2.36)
    *   每个节点 6.4 TB 本地 NVME SSD 存储
    *   每个节点 36 个核心
    *   每个节点 384 GB 内存

## 可选基础结构

### 存储

除了 HPC 配置随附的 NVME SSD 存储，您还可以在每个卷的 32k IOPS 时附加块存储卷，并由 Oracle 高性能 SLA 提供支持。如果您使用我们的解决方案来启动基础设施，则默认情况下在 /mnt 的 NVME SSD 存储上安装 nfs-share。还可以在 NVME SSD 存储或块存储上安装您自己的并行文件系统，具体取决于您的性能要求。

### 可视化器节点

您可以根据自己的需求创建可视化器节点，例如 GPU VM 或 BM 计算机。此可视化器节点可以是堡垒主机，也可以是独立的。可视化器节点可以放置在专用或公共子网中，具体取决于工作负荷的安全要求。

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月