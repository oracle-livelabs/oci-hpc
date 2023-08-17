# 在 HPC 集群中运行 OpenFoam 项目

## 简介

在其中设置 HPC 硬件和部署应用软件可能需要很长时间，并包括各种复杂的操作。在此演示中，所有这些任务都是在 OCI 市场映像中自动完成的，并且预配后立即提供功能完备的 HPC 环境以及 OpenFoam 模拟软件。只需在功能完备的 HPC 环境中执行负载并运行可视化应用（如 Paraview）即可呈现输出。

估计实验室时间：60 分钟

### 引用体系结构

![市场](images/29_Architecture.png)

### 关于 OpenFoam：

计算流体动力学 (Computational Fluid Dynamics，CFD) 是使用数值分析模拟流体运动和热转移。CFD 软件（例如 OpenFOAM）可以用作许多工程行业的强大工具，用于改进设计、可视化影响和检测系统中效率低下现象。但是，处理模拟所需的计算能力可能非常繁重。超计算机和 HPC 集群通常用于在合理的时间范围内执行大型复杂模型。在此实验室中，您将使用 Oracle HPC 集群中的开源 CFD 软件 OpenFOAM 计算并呈现摩托车的空气动力学模型

### 基础结构术语：

1.  Worker 节点：HPC 计算实例，提供执行计算模拟或其他工程负载的处理能力。在此演示中，计算配置 BM.HPC2.36 节点是 worker 节点。
2.  Head 节点：计算实例，其中可以调度并提交所有计算作业以在 Worker 节点上运行。多次头节点和堡垒节点可以是同一台计算机。在本演示中，我们将预配堡垒节点。
3.  GPU 节点：具有 GPU 处理器的专用计算实例，用于呈现计算负载的输出。此演示未预配 GPU 计算机。
4.  堡垒节点：具有公共 IP 的计算实例，用作连接到通常位于专用网络中的 worker 节点的入口点。
5.  RoCE 网络：基于融合以太网的 RDMA (RoCE) 是一种网络协议，允许通过以太网网络进行远程直接内存访问 (Remote Direct Memory Access，RDMA)。

### 目标

1.  使用 OCI 市场映像预配功能完备的 HPC 环境
2.  部署名为 OpenFoam 的基于 CFD 的模拟应用程序
3.  执行 Openfoam 工作量
4.  使用 Paraview 可视化应用程序渲染输出。

### 前提条件

开始使用此练习之前，请确保具有以下权限：

1.  授权计算服务使用以下策略代表您管理标记名称空间：
    
    “允许服务 compute\_management 在租户中使用 tag-namespace”
    
2.  在此演示中仅允许裸金属配置 BM.HPC2.36。在同一可用性域中，至少需要 2 个计算配置。
    
3.  堡垒服务器的 VM 计算映像。不必位于与 HPC 节点相同的可用性域中。
    
4.  用户身份证明应具有限额，才能创建或使用具有专用和公共子网的现有 VCN。
    
5.  访问市场映像“CFD Ready 集群”。此演示中使用的市场映像版本为（版本：20200625）。
    

## 任务 1：启动市场图像

1.  在用户启动市场映像之前，请检查配置为 BM.HPC2.36 的 2 个或更多 HPC 节点所在的可用性域。转至菜单 -> 监管 -> 限制、限额和使用量 ![市场](images/01-Service_Limits.png)

2.  转至“菜单”->“市场”->“应用程序” ![市场](images/02-Marketplace.png)

3.  在搜索框类型 "cfd" 中 ![市场](images/03-Search_marketplace.png)

4.  单击图像，选择图像，然后单击“启动堆栈”按钮。检查您是否位于正确的区间中。 ![市场](images/04-Launch_Stack.png)

5.  启动堆栈后，填写详细信息以创建资源，包括 HPC worker 节点、堡垒服务器、网络组件和 VCN 服务器。堆栈还将要求输入以部署 openMPI 和 openFoam 等程序包。
    
6.  在下面提供堆栈的名称 ![市场](images/05-Create_Stack01.png)
    

7.  选择具有可用于预配的 HPC 节点的可用性域。提供连接到堡垒服务器所需的 SSH 公共密钥。 ![市场](images/06-Create_Stack02.png)
    
8.  选择要预配堡垒服务器的可用性域。它可以不同于 HPC 节点上的可用性域。选择您选择的堡垒主机配置。启用此复选框可在堡垒服务器上安装 VNC 服务器并提供以后进行 VNC 连接所需的密码。 ![市场](images/07-Create_Stack03.png)
    

9.  对于 Worker 节点，选择的唯一选项是 BM.HPC2.36。要完成预配，您需要至少 2 个 HPC 计算配置。在下面的屏幕中选择 2 个或更多计算节点。Rest 所有选项都可以保留为默认值。 ![市场](images/08-Create_Stack04.png)

10.  在“Network（网络）”选项下，使用堆栈中传递的默认值创建一个新 VCN。此演示将创建通过此堆栈创建的新 VCN。但是，也可以选择使用现有 VCN。 ![市场](images/09-Create_Stack05.png)

11.  对于 "File System"（文件系统）部分，请使用以下选项在上面创建 NFS 挂载和安装 gluster 文件系统。 ![市场](images/10-Create_Stack06.png)

12.  单击 "INSTALL OPENFOAM" 复选框以在此 HPC 堆栈中安装 OpenFoam 应用程序。 ![市场](images/11-Create_Stack07.png)

13.  在堆栈的最后一页中，验证输入的详细信息，如果一切正常，请单击“Create（创建）”按钮，开始预配基础结构和软件安装。 ![市场](images/12-Create_Stack08.png)

14.  验证 HPC 堆栈安装进度 ![市场](images/13-StackJob.png)

15.  预配在大约 20 分钟内完成后，日志文件应包含以下消息。可以在“菜单”->“资源管理器”->“堆栈”（单击您的堆栈名称）->“作业”（单击作业名称链接可查看完整日志）找到日志 ![市场](images/14-StackJobDetails.png)

## 任务 2：验证设置

1.  单击屏幕左侧的 "Associate Resources"（关联资源）链接可查看此堆栈预配的基础结构资源。可以在“菜单”->“资源管理器”->“堆栈”（单击您的堆栈名称）->“作业”（单击作业名称链接）->“关联资源” ![市场](images/15-Stack_Resources.png)

2.  在“计算”->“实例”下，将预配 3 个计算实例、专用网络中的 2 个 HPC 节点和具有公共 IP 地址的 1 个堡垒服务器。 ![市场](images/16-Compute_Details.png)

3.  使用在上面的堆栈中上载了公钥的 ssh 私钥以 opc 用户身份使用公共 IP 地址连接到堡垒服务器。使用此方法从 Linux/Mac Desktop 连接。对于 Windows 桌面，使用 Putty 并提供 ppk 私钥进行连接。 ![市场](images/17-Bastion_ssh.png)

4.  验证在 2 个 HPC 节点和堡垒服务器上挂载的共享 NFS 存储。此 NFS 存储包含用于运行工作负荷的 OpenFoam 软件。 ![市场](images/18-Bastion_storage.png)

5.  OpenFoam 应用程序托管在 "/mnt/gluster-share/work" 文件夹下。检查包含运行工作负荷的 2 个 HPC 节点的专用 IP 地址的主机文件。 ![市场](images/19-Bastion_openfoam.png)

6.  以下输出显示了端口 5901 上正在运行的 VNC 服务器。此端口将用于与堡垒主机建立 VNC 连接。 ![市场](images/20-Bastion_VNCdetails.png)

7.  根据允许端口 5901 的入站规则从堡垒服务器的安全列表中进行检查。如果 VNC 在任何其他端口上运行，并且不允许该端口在安全列表中入站通信，请为同一端口创建新安全规则。 ![市场](images/21-SecList_OpenVNC_Port.png)

8.  验证 HPC 节点的名称 ![市场](images/22-ListHPCNodes.png)

## 任务 3：运行模拟工作量并渲染输出

1.  在堡垒服务器上，运行使用 Paraview 软件包呈现输出所需的以下命令。
    
        sudo yum install -y mesa-libGLU
        cd /mnt/gluster-share
        curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" https://www.paraview.org/paraview-downloads/download.php > file.tar.gz
        tar -xf file.tar.gz
        

2.  从堡垒服务器连接到其中一个 HPC 节点并执行负载
    
        ssh 172.16.1.2
        cd /mnt/gluster-share/work/
        ./Allrun 36
        
    
        ./Allrun 36
        Cleaning /mnt/gluster-share/work case
        Mesh Dimensions: (40 16 16)
        Cores:36: 6, 6, 1
        Running surfaceFeatures on /mnt/gluster-share/work
        Running blockMesh on /mnt/gluster-share/work
        Running decomposePar on /mnt/gluster-share/work
        Running snappyHexMesh
        Running patchsummary
        Running potentialFoam
        Running simpleFoam
        Running reconstructParMesh on /mnt/gluster-share/work
        Running reconstructPar on /mnt/gluster-share/work
        219.95
        [opc@inst-quqyz-accurate-swan work]$
        

3.  工作量成功完成后，按如下方式在计算机上配置 VNC 客户机。提供堡垒服务器和 VNC 端口的公共 IP ![市场](images/24-VNC_Connection.png)

4.  可选，如果不允许您打开 VNC 端口 5901，或者由于安全原因要为此端口创建 ssh 隧道，请使用以下命令在端口 5901 上创建 ssh 隧道，而不在安全列表中打开端口

使用终端窗口中的以下命令从手提电脑/桌面创建隧道。此处将在 SSH 端口 22 上进行端口 5901 的通信，IP 地址 150.136.41.3 是堡垒服务器的公共 IP 地址。

      ```
      ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3
      ```
    

5.  请勿关闭以上 ssh 隧道终端窗口。现在启动 VNC 会话，这次而不是 IP 地址在端口 5901 上使用 "localhost"，即使该端口未在子网的安全列表中打开也是如此。 ![市场](images/28-ssh_Tunnel.png)

6.  从堡垒服务器中启动 Paraview 应用程序
    
        cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        ./paraview
        
7.  在 Paraview 应用程序窗口中，文件 -> 打开 -> 路径 "/mnt/gluster-share/work"，然后选择文件名 motorbike.foam。它将是零字节文件，应该没问题。 ![市场](images/25-Paraview_Menu.png)
    

8.  在窗口左侧的“Properties（属性）”选项卡下，选择“Mesh Regions（网格区域）”以选择所有值，然后取消选择不以 motorBike\_ 前缀开头的顶部值。确保已选择以 motorBike\_ 开头的所有值。单击“Apply（应用）”按钮，将弹出一些错误，忽略弹出窗口以在 VNC 控制台中查看图像的呈现。 ![市场](images/26-Mesh_Regions.png)

9.  屏幕上将呈现如下图像。根据某些显示设置，屏幕上的图像可能看起来有点不同。 ![市场](images/27-Image_Rendering.png)

全部完成！这将完成在 HPC 集群上运行 OpenFoam 应用程序的演示。

_您现在可以继续下一练习_

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者/日期** - Harrison Dvoor，2020 年 10 月

# <<<<<<< 标头

> > > > > > > 上游/主节点