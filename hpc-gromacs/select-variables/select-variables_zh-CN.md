# 选择变量

## 简介

在本练习中，您将为堆栈填充变量。

估计实验室时间：10 分钟

## 任务：选择变量

1.  单击 ![](./images/next.png) 并填充变量。
    
    **GPU 节点**：
    
    *   GPU 计算节点的形状：将用于运行 Gromacs 的计算节点的形状。选择裸金属配置 BM.GPU2.2 或 BM.GPU3.8 以获得最佳性能。
    *   可用性域：实例和块存储卷的增加。AD 必须具有可用的 GPU。
    *   GPU 节点计数：所需的 GPU 节点数。
    *   GPU 的 VNC 类型：头节点的可视化类型：无、VNC 或 X11VNC。
    *   VNC 会话的口令：使用前/后节点上的 VNC 会话的口令。
    
    **块选项**：
    
    *   块 VOLUME 大小 (GB)：共享块存储卷的大小。
    
    **Gromacs** :
    
    *   URL TO DOWNLOAD Gromacs:Gromacs 2020.1 已编译二进制文件的 URL（如果希望稍后下载，请将 URL 替换为其他版本或留空）。
    *   URL TO DOWNLOAD A MODEL TARBALL：您希望运行的模型的 URL（将 URL 替换为其他模型，如果希望稍后下载，请留空）。
    *   URL TO DOWNLOAD VMD VISUALIZATION SOFTWARE：用于下载 VMD 1.9.3 以可视化 Gromacs 模型的 URL（将 URL 替换为其他可视化软件，如果希望稍后下载，请留空）。
2.  单击 ![](./images/next.png)。复查信息，然后单击 ![](./images/create.png)
    

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月