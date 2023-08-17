# 在 OCI 上的标准 VM 中运行 OpenFoam 项目

## 简介

以下任务旨在帮助评估 Oracle Cloud Infrastructure 中的 OpenFOAM CFD 软件。完成任务以自动下载和配置 OpenFOAM。OpenFOAM 是免费的开源 CFD 软件，主要由 OpenCFD Ltd 自 2004 年起发布和开发。它拥有来自商业和学术组织的大部分工程和科学领域的大用户群。OpenFOAM 提供广泛的功能来解决各种问题，包括化学反应、动荡和热转移、声学、固体力学和电磁学等复杂的流体流动。

系统将指导您在基本 VM.Standard 2.1 配置中安装 OpenFoam，但对于实际部署，较大的配置（如 BM.HPC2.36）是合适的。某些高性能配置仅在特定区域和可用性域中可用。

我们将创建两个节点，即公共子网上群集的头节点和专用子网中的 worker 计算节点。为了访问 worker 节点，我们将首先创建 headnode，然后在 headnode 上生成一个 ssh 密钥，并在创建 worker 节点时使用该公钥。

要**日志问题**，请单击[此处](https://github.com/oracle/learning-library/issues/new)转至 github oracle 系统信息库问题提交表单。

估计时间：60 分钟

### 目标

作为一名开发人员，数据工程师，

1.  手动部署高性能计算实例
    
2.  创建集群网络
    
3.  创建网络文件系统
    
4.  设置 VNC
    
5.  安装 OpenFoam 和 Paraview
    

### 前提条件

*   具有创建实例 VM 标准 2.1 或 BM.HPC2.36 配置的权限的 Oracle Cloud Infrastructure 账户。

### 基础结构术语

*   Worker 节点：HPC 计算实例，提供执行计算模拟或其他工程负载的处理能力。在此演示中，计算配置 BM.HPC2.36 节点是 worker 节点。
    
*   Head 节点：计算实例，其中可以调度并提交所有计算作业以在 Worker 节点上运行。多次头节点和堡垒节点可以是同一台计算机。在本演示中，我们将预配堡垒节点。
    
*   堡垒节点：具有公共 IP 的计算实例，用作连接到通常位于专用网络中的 worker 节点的入口点。
    
*   RoCE 网络：基于融合以太网的 RDMA (RoCE) 是一种网络协议，允许通过以太网网络进行远程直接内存访问 (Remote Direct Memory Access，RDMA)。
    

## 任务 1：预配 Oracle 虚拟云网络

1.  在创建实例之前，我们需要配置虚拟云网络。选择左上角的菜单，然后选择**网络**和**虚拟云网络**
    
    ![](./images/vcn.png " ")
    
    ![](./images/create_vcn.png " ")
    
    ![](./images/vcn_content.png " ")
    
2.  在下一页上，为您的 VCN 选择以下项：
    
    *   名称
        
    *   区间
        
    *   CIDR 块（示例：10.0.0.0/16）
        
3.  滚动到底部并单击 "Create VCN"
    
4.  创建子网
    
    1.  公共子网
        
        *   名称：即 hpc\_public
            
        *   子网类型：区域
            
        *   CIDR 块：10.0.0.0/24
            
        *   路由表："Default Route table"
            
        *   子网访问：公共子网
            
        *   安全列表："Default Security List"
            
    2.  专用子网
        
        *   名称：hpc\_private
            
        *   子网类型：区域
            
        *   CIDR 块：10.0.3.0/24
            
        *   路由表：选择在上一步中创建的路由表
            
        *   子网访问：专用子网
            
        *   安全列表：选择在上一步中创建的安全列表
            
5.  单击**创建子网**
    
    ![](./images/create_subnet.png " ")
    
    ![](./images/create_subnet_content.png " ")
    
6.  单击您创建的 `hpc_vcn`，然后在页面左侧的**资源**菜单上，选择**互联网网关**，创建互联网网关。
    
    ![](./images/create_IG.png " ")
    
    ![](./images/create_IG_content.png " ")
    
    > **注：**这将创建 Internet 网关，并且需要将其与路由表关联。在这种情况下，由于默认路由表将用于公共子网，因此互联网网关应与该路由表关联。
    
7.  将路由规则添加到路由表。在页面左侧的**资源**菜单上，选择**用于 `hpc_vcn` 的默认路由表**，单击**添加路由规则**
    
    *   目标类型：Internet 网关
        
    *   目标 CIDR 块：0.0.0.0/0
        
    *   区间中的目标 Internet 网关：您创建的 Internet 网关
        
    
    ![](./images/route_table.png " ")
    
    ![](./images/route_rules.png " ")
    

## 任务 2：创建集群节点

我们将为此任务创建两个节点，即公共子网上的群集的 **Headnode** 和专用子网中的 **a Worker compute node** 。为了访问 worker 节点，我们将首先创建 headnode，然后在 headnode 上生成一个 ssh 密钥，并在创建 worker 节点时使用该公钥。

> **注：**对于此任务，我们将仅使用基本 VM.Standard2.1 配置，但对于实际部署，较大的配置（如 BM.HPC2.36）是合适的。某些高性能配置仅在特定区域和可用性域中可用。

1.  创建头节点
    
    *   名称：即 hpc\_head
        
    *   映像或操作系统：最新版本的 Oracle Linux（默认）
        
    *   可用性域：可用于 VM.Standard 2.1 配置的域
        
    *   实例配置：
        
        *   VM.Standard 2.1
            
        *   任何其他配置（如果有的话，最好使用 BM.HPC2.36）
            
    *   虚拟云网络：之前创建的 VCN
        
    *   子网：之前创建的公共子网
        
    *   SSH 密钥：附加您的公共密钥文件
        
    
    1.  在左上角的菜单中，选择“Compute（计算）”并创建实例。
        
        ![](./images/compute.png " ")
        
        ![](./images/compute_bm.png " ")
        
        ![](./images/compute_vm.png " ")
        
    2.  通过 SSH 连接到机头节点并生成特定于集群的 SSH 密钥以进行通信。
        
            $ <copy>ssh -i <ssh_key> opc@<headnode_ip_address></copy>
            
            $ <copy>ssh-keygen</copy>
            
        
        > **注：**请勿更改 ssh 密钥文件位置 (/home/opc/.ssh/id\_rsa)，并在询问口令短语（两个）时按 Enter 键。
        
        运行并复制用于创建 worker 节点的整个字符串。
        
            $ <copy>cat ~/.ssh/id_rsa.pub</copy>
            
2.  创建员工节点
    
    1.  在左上角的菜单中，选择“Compute（计算）”并创建包含以下详细信息的实例：
        
        *   名称：即 hpc\_worker1
            
        *   映像或操作系统：最新版本的 Oracle Linux（默认）
            
        *   可用性域：可用于 VM.Standard 2.1 配置的域
            
        *   实例配置：
            
            *   VM.Standard 2.1
                
            *   任何其他配置（如果有的话，最好使用 BM.HPC2.36）
                
        *   虚拟云网络：之前创建的 VCN
            
        *   子网：之前创建的专用子网
            
        *   SSH 密钥：从上面的步骤复制了公共密钥字符串。
            
    2.  SSH 到 worker 节点。返回到已登录到机头节点的控制台，然后从机头节点将专用 IP 地址和 ssh 带到 worker 节点
        
            $ <copy>ssh opc@10.x.x.x</copy>
            

## 任务 3：设置 NAT 网关

> **注：**这仅适用于 Worker 节点。

1.  选择 worker 节点，然后单击左侧的**资源**菜单中的**附加的 VNIC**
    
2.  选择**编辑 VNIC**
    
3.  如果选中**跳过源/目标检查**，然后单击**更新 VNIC**
    
    ![](./images/nat_gateway.png " ")
    

## 任务 4：挂载驱动器

> **注：**仅当节点配置附加了 NVMe 时（BM.HPC2.36 有一个，而不是 VM.Standard2.1），HPC 计算机具有本地 NVMe 存储，但默认情况下不会挂载该存储。如果使用 VM.Standard2.1，请跳至步骤 5

1.  通过 SSH 进入机头节点并运行以下命令
    
        $ <copy>lsblk</copy>
        
    
    驱动器应使用左侧的 NAME 列出（可能是 nvme0n1，如果不同，请在下一个命令中更改它）。
    
    机头节点将具有安装和型号的共享驱动器。这将在所有不同的 worker 节点之间共享。每个 worker 节点还将挂载要在本地在 NVMe 驱动器上运行的驱动器。在本示例中，共享目录将为 500 GB，但可以随时更改该目录。
    
2.  对 worker 节点上的驱动器进行分区（可选）
    
        $ <copy>sudo yum -y install gdisk</copy>
        
    
        $ <copy>sudo gdisk /dev/nvme0n1</copy>     $ > n    # Create new partition
        $ > 1    # Partition Number
        $ >      # Default start of the partition
        $ > +500G # Size of the shared partition
        $ > 8300  # Type = Linux File System
        $ > n     # Create new partition
        $ > 2     # Partition Number
        $ >       # Default start of the partition
        $ >       # Default end of the partition, to fill the whole drive
        
        $ > 8300  # Type = Linux File System
        
        $ > w      # Write to file
        $ > Y      # Accept Changes
        
3.  设置 worker 节点上的驱动器格式
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1</copy>
        
4.  设置头节点上的分区格式
    
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p1</copy>
        $ <copy>sudo mkfs -t ext4 /dev/nvme0n1p2</copy>
        
5.  创建目录并挂载驱动器
    
6.  Headnode(local and share)（Headnode（本地和共享）：选择 /mnt/share 作为 500G 分区的挂载目录，选择 /mnt/local 作为更大的分区。
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1p1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1p2 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        
7.  头节点（共享）：
    
        $ <copy>sudo mkdir /mnt/share</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/share</copy>
        $ <copy>sudo chmod 777 /mnt/share</copy>
        
8.  Worker 节点：选择 /mnt/local 作为整个驱动器的挂载目录。
    
        $ <copy>sudo mkdir /mnt/local</copy>
        $ <copy>sudo mount /dev/nvme0n1 /mnt/local</copy>
        $ <copy>sudo chmod 777 /mnt/local</copy>
        

## 任务 5：创建网络文件系统

1.  创建装载目标。在左上角的菜单上，单击**文件存储**和**挂载目标**。
    
    *   新挂载目标名称：输入名称（例如：openfoam\_fs）
        
    *   虚拟云网络：选择上面创建的 VCN
        
    *   子网：选择公共 VCN
        
2.  Headnode 位于公共子网中，我们将保持防火墙状态并通过以下方式添加异常错误：  
    
        <copy>sudo firewall-cmd --permanent --zone=public --add-service=nfs</copy>
        <copy>sudo firewall-cmd --reload</copy>
        
3.  单击已创建的挂载目标，然后单击导出路径，然后单击挂载命令并复制应如下所示的以下命令：
    
        <copy>sudo yum install nfs-utils</copy>
        <copy>sudo mkdir -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        
4.  在 worker 节点上，由于它们位于安全列表限制访问的专用子网中，因此可以完全禁用防火墙。然后，我们可以像上面那样安装 nfs-utils 工具并挂载 nfs。
    
        <copy>sudo systemctl stop firewalld</copy>
        <copy>sudo yum -y install nfs-utils</copy>
        <copy>sudo mkdir  -p /mnt/share</copy>
        <copy>sudo mount <fss-ip-address>:/<ExportPathName> /mnt/share</copy>
        

## 任务 6：安装 OpenFOAM

1.  **连接所有 worker 节点**  
    
    每个 worker 节点都需要能够与所有 worker 节点通信。SSH 通信正常，但是如果所有主机都不在已知主机文件中，大多数应用程序都会出现问题。要对 VCN 中具有地址的节点禁用已知主机检查，可以使用以下命令停用。如果子网中的地址不同，则可能需要稍加修改。将以下代码块放入 shell 脚本并运行脚本。
    
        <copy>for i in 0 1 2 3
        do
             echo Host 10.0.$i.* | sudo tee -a ~/.ssh/config
             echo "    StrictHostKeyChecking no" | sudo tee -a ~/.ssh/config
        done</copy>
        
2.  **创建机械师**  
    
    头节点上的 OpenFOAM 不会自动知道哪些计算节点可用。您可以在 `/mnt/share/machinelist.txt with` 上创建一个计算机文件，其中包含所有节点的专用 IP 地址以及可用的 CPU 数。还应包括头节点。条目的格式为 `private-ip-address cpu=number-of-cores`
    
        <copy>10.0.0.2 cpu=1
        10.0.3.2 cpu=1</copy>
        
3.  **头节点**
    
    从源安装，在下一个命令中修改 tarballs 的路径。此示例具有基础 OpenFOAM 源。还测试了来自 ESI 的 OpenFOAM。要在不同的计算节点之间共享安装，请安装在网络文件系统上。
    
        <copy>$ sudo yum groupinstall -y 'Development Tools'
        $ sudo yum -y install devtoolset-8 gcc-c++ zlib-devel openmpi openmpi-devel
        $ cd /mnt/share
        $ wget -O - http://dl.openfoam.org/source/7 | tar xvz
        $ wget -O - http://dl.openfoam.org/third-party/7 | tar xvz
        $ mv OpenFOAM-7-version-7 OpenFOAM-7
        $ mv ThirdParty-7-version-7 ThirdParty-7
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | sudo tee -a ~/.bashrc
        $ echo export $ LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc
        $ cd /mnt/share/OpenFOAM-7
        $ ./Allwmake -j</copy>
        
4.  **员工节点**  
    
        $ <copy>sudo yum -y install openmpi openmpi-devel
        $ cd /mnt/share
        $ export PATH=/usr/lib64/openmpi/bin/:/usr/lib64/qt5/bin/:$PATH
        $ echo export PATH=/usr/lib64/openmpi/bin/:\$PATH | $ sudo tee -a ~/.bashrc
        $ echo export $LD_LIBRARY_PATH=/usr/lib64/openmpi/lib/:\$LD_LIBRARY_PATH | sudo tee -a ~/.bashrc
        $ echo source /mnt/share/OpenFOAM-7/etc/bashrc | $ sudo tee -a ~/.bashrc
        $ sudo ln -s /usr/lib64/libboost_thread-mt.so /usr/lib64/libboost_thread.so
        $ source ~/.bashrc</copy>
        

## 任务 7：运行模拟工作量并渲染输出

1.  在 Headnode 上，运行使用 Paraview 软件包呈现输出所需的以下命令。
    
        $ <copy>sudo yum install -y mesa-libGLU
        $ cd /mnt/share
        $ curl -d submit="Download" -d version="v4.4" -d type="binary" -d os="Linux" -d downloadFile="ParaView-4.4.0-Qt4-Linux-64bit.tar.gz" $ https://www.paraview.org/paraview-downloads/download.php >  file.tar.gz
        $ tar -xf file.tar.gz</copy>
        
2.  在 headnonde 上，运行以下命令以设置 VNC 服务器：  
    
        $ <copy>sudo yum -y groupinstall 'Server with GUI'
        $ sudo yum -y install tigervnc-server mesa-libGL
        $ sudo mkdir /home/opc/.vnc/
        $ sudo chown opc:opc /home/opc/.vnc
        $ echo "HPC_oci1" | vncpasswd -f > /home/opc/.vnc/passwd
        $ chown opc:opc /home/opc/.vnc/passwd
        $ chmod 600 /home/opc/.vnc/passwd
        $ /usr/bin/vncserver</copy>
        
3.  在其中一个 worker 节点中，使用 /mnt/share/work 中的 [scripts](../scripts/motorbike_RDMA.tgz) 下载此 zip。使用 `tar -xf motorbike_RDMA.tgz` 解压缩文件
    
4.  在执行工作量之前，需要编辑 allrun 文件以匹配我们构建的体系结构。首先，我们将从 OpenFOAM 安装程序文件夹中移动该文件夹
    
        $ <copy>model_drive=/mnt/share
        $ sudo mkdir $model_drive/work
        $ sudo chmod 777 $model_drive/work
        $ cp -r $FOAM_TUTORIALS/incompressible/simpleFoam/motorBike $model_drive/work
        $ cd /mnt/share/work/motorBike/system</copy>
        
5.  编辑文件系统/decomposeParDict 并将此行 numberOfSubdomains 6；更改为 numberOfSubdomains 12；或需要多少进程。然后，在 hierarchicalCoeffs 块中，将 n 从 n 更改为 n (3 2 1)；将 n 更改为 n (4 3 1)；如果将这 3 个值相乘，则应得到 numberOfSubdomains
    
    使用配置 1 VM.Standard2.1 worker 节点运行：
    
        =========                 |
        \\      /  F ield         | OpenFOAM: The Open Source CFD Toolbox
         \\    /   O peration     | Website:  https://openfoam.org
          \\  /    A nd           | Version:  7
           \\/     M anipulation  |
        \*---------------------------------------------------------------------------*/
        FoamFile
        {
             version     2.0;
             format      ascii;
             class       dictionary;
             object      decomposeParDict;
        }
        
        // * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * //
        
        numberOfSubdomains 2;
        
        method          hierarchical;
        // method          ptscotch;
        
        simpleCoeffs
        {
             n               (4 1 1);
             delta           0.001;
        }
        
        hierarchicalCoeffs
        {
             n               (2 1 1);
             delta           0.001;
             order           xyz;
        }
        
        manualCoeffs
        {
             dataFile        "cellDecomposition";
        }
        
        // ************************************************************************* //
        
6.  下次编辑 /mnt/share/work/motorBike 中的 Allrun 文件以如下所示
    
        <copy>#!/bin/sh
        cd ${0%/*} || exit 1    # Run from this directory
        NP=$1
        install_drive=/mnt/share
        # Source tutorial run functions
        . $WM_PROJECT_DIR/bin/tools/RunFunctions
        
        # Copy motorbike surface from resources directory
        cp $FOAM_TUTORIALS/resources/geometry/motorBike.obj.gz constant/triSurface/
        cp $install_drive/machinelist.txt hostfile
        
        runApplication surfaceFeatures
        
        runApplication blockMesh
        
        runApplication decomposePar -copyZero
        echo "Running snappyHexMesh"
        mpirun -np $NP -machinefile hostfile snappyHexMesh -parallel -overwrite > log.snappyHexMesh
        ls -d processor* | xargs -I {} rm -rf ./{}/0
        ls -d processor* | xargs -I {} cp -r 0 ./{}/0
        echo "Running patchsummary"
        mpirun -np $NP -machinefile hostfile patchSummary -parallel > log.patchSummary
        echo "Running potentialFoam"
        mpirun -np $NP -machinefile hostfile potentialFoam -parallel > log.potentialFoam
        echo "Running simpleFoam"
        mpirun -np $NP -machinefile hostfile $(getApplication) -parallel > log.simpleFoam
        
        runApplication reconstructParMesh -constant
        runApplication reconstructPar -latestTime
        
        foamToVTK
        touch motorbike.foam</copy>
        
7.  确保位于 worker 节点中并执行工作量：
    
        $ <copy>ssh worker_node_IP
        $ cd /mnt/share/work/
        $ ./Allrun 2
        
        $ ./Allrun 2
        $ Cleaning /mnt/share/work case
        $ Mesh Dimensions: (40 16 16)
        $ Cores:36: 6, 6, 1
        $ Running surfaceFeatures on /mnt/share/work
        $ Running blockMesh on /mnt/share/work
        $ Running decomposePar on /mnt/share/work
        $ Running snappyHexMesh
        $ Running patchsummary
        $ Running potentialFoam
        $ Running simpleFoam
        $ Running reconstructParMesh on /mnt/share/work
        $ Running reconstructPar on /mnt/share/work
        219.95</copy>
        
8.  工作量成功完成后，按如下方式在计算机上配置 VNC 客户机。提供堡垒服务器和 VNC 端口的公共 IP
    
    ![](./images/24-VNC_Connection.png " ")
    
9.  可选，如果不允许您打开 VNC 端口 5901，或者由于安全原因要为此端口创建 ssh 隧道，请使用以下命令在端口 5901 上创建 ssh 隧道，而不在安全列表中打开端口
    
10.  使用终端窗口中的以下命令从手提电脑/桌面创建隧道。此处将在 SSH 端口 22 上进行端口 5901 的通信，IP 地址 150.136.41.3 是堡垒服务器的公共 IP 地址。
    
        $ <copy>ssh -L 5901:localhost:5901 -i Dropbox/amar_priv_key -N -f -l opc 150.136.41.3</copy>
        
11.  请勿关闭以上 ssh 隧道终端窗口。现在启动 VNC 会话，这次而不是 IP 地址在端口 5901 上使用 "localhost"，即使该端口未在子网的安全列表中打开也是如此。
    
    ![](./images/28-ssh_Tunnel.png " ")
    
12.  从堡垒服务器中启动 Paraview 应用程序
    
        $ <copy>cd /mnt/gluster-share/ParaView-4.4.0-Qt4-Linux-64bit/bin/
        $ ./paraview</copy>
        
13.  在 Paraview 应用程序窗口中，文件 -> 打开 -> 路径 "/mnt/gluster-share/work"，然后选择文件名 motorbike.foam。它将是零字节文件，应该没问题。
    
    ![](./images/25-Paraview_Menu.png " ")
    
14.  在窗口左侧的“Properties（属性）”选项卡下，选择“Mesh Regions（网格区域）”以选择所有值，然后取消选择不以 motorBike\_ 前缀开头的顶部值。确保已选择以 motorBike\_ 开头的所有值。单击“Apply（应用）”按钮，将弹出一些错误，忽略弹出窗口以在 VNC 控制台中查看图像的呈现。
    
    ![](./images/26-Mesh_Regions.png " ")
    
15.  屏幕上将呈现如下图像。根据某些显示设置，屏幕上的图像可能看起来有点不同。
    
    ![](./images/27-Image_Rendering.png " ")
    

全部完成！这将完成在 OCI 上的标准 VM 上运行 OpenFoam 应用程序的演示。

_恭喜！您已成功完成研讨会_

以下是有关管理高性能计算实例的详细信息。有关完整的命令参考，请查看[此处](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc)的 OCI 文档。

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者/日期** - Harrison Dvoor，2021 年 1 月