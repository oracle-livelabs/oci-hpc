# 启动 HPC 集群网络

## 简介

可通过多种方式启动 HPC 集群网络，本解决方案指南将介绍两种不同的方法：

*   通过市场
*   手动取决于您的操作系统，您需要使用特定方法。如果使用 HPC 集群网络市场映像或 OCI HPC CN Terraform 脚本，则仅适用于 Oracle Linux 7。如果要使用 CentOS、Ubuntu 或其他 OS，则需要手动配置。

估计实验室时间：30 分钟

## **选项 1** ：通过市场创建集群网络

市场包含可用于基础设施的应用和映像。对于希望使用 Oracle Linux 的客户，HPC 集群网络映像可用，可以直接从市场中启动。我们建议启动 [CFD Ready Cluster](https://cloudmarketplace.oracle.com/marketplace/en_US/listing/75645211) ，该集群将包含 CFD 所需的库。

1.  在市场中，选择右上角的“Get App”。 ![](images/get-app.png " ")
    
2.  选择 OCI 区域，然后单击“Sign In（登录）”。 ![](images/sign-in.png " ")
    
3.  登录后，验证 HPC 集群映像的版本，然后选择将启动集群的区间。接受条款和条件，然后启动堆栈。
    
    ![](images/launch-stack.png " ")
    
4.  填写堆栈的剩余详细信息：
    
    *   为计算配置和堡垒选择所需的 AD。
    *   复制 - 粘贴您的公共 SSH 密钥
    *   键入集群的计算实例数
    *   取消选中“安装 OpenFOAM”
    *   如果需要的共享磁盘空间超过 6TB，请检查 GlusterFS 并选择所需的服务器数。（每个服务器 6TB）
5.  单击“创建”。
    
6.  导航到“Terraform 操作”，然后单击“应用”。这将启动 CN 预配。
    
    ![](images/apply.png " ")
    
7.  等待作业显示“成功”，然后导航到“输出”以获取堡垒和计算节点专用 IP。
    

## **选项 2** ：通过手动配置创建群集网络

市场包含可用于基础设施的应用和映像。对于希望使用 Oracle Linux 的客户，您可以按如下方式手动创建群集网络：

1.  选择右上角的 OCI 区域。
    
2.  在主菜单中，选择 "Networking and Virtual Cloud Networks"（网络和虚拟云网络）
    
3.  单击“Start VCN Wizard（启动 VCN 向导）”，然后选择“VCN with Internet Connectivity（具有 Internet 连接的 VCN）”
    
4.  选择并命名右侧区间，并将 172.16.0.0/16 用作 VCN CIDR，172.16.0.0/24 表示公共子网，172.16.1.0/24 表示专用子网。单击**下一步**，然后单击**创建**。
    
5.  在主菜单中，依次选择“Compute（计算）”、“Instance（实例）”和“Create Instance（创建实例）”
    
6.  选择 **Change Image** ，然后在 "Oracle Image" 选项卡下选择 "Oracle Linux 7 - HPC Cluster Networking Image"。
    
7.  选择可以在其中启动 BM.HPC2.36 实例的可用性域
    
8.  将裸金属和专业下面的配置更改为 BM.HPC2.36
    
9.  选择您创建的 VCN 和公共子网。
    
10.  添加公共密钥以连接到实例。此密钥将用于所有计算实例。
    
11.  计算机启动后，单击创建的实例。在“More Actions（更多操作）”下，选择“Create Instance Configuration（创建实例配置）”。您现在可以在“More Actions（更多操作）”下终止实例。
    
12.  在主菜单中，依次选择“Compute（计算）”和“Cluster Networks（集群网络）”
    
13.  单击 "Create Cluster Network"（创建群集网络）并填写所有选项。使用刚刚创建的 VCN、专用子网和实例配置。选择可在其中启动 BM.HPC2.36 实例的 AD。
    
14.  启动集群网络。
    
15.  加载期间，在主菜单、计算和实例下创建另一个实例。
    
16.  将其放在刚刚创建的公共子网中，使用您的公共密钥和配置应为 VM.Standard2.1 或类似配置。这将是我们将用于连接到集群的堡垒。
    
17.  将堡垒上的群集键固定在 /home/opc/.ssh/cluster\_key 上，并将其复制到 /home/opc/.ssh/id\_rsa
    
18.  通过以下命令在堡垒上安装预配工具：
    
        sudo rpm -Uvh https://objectstorage.us-ashburn-1.oraclecloud.com/n/hpc/b/rpms/o/oci-hpc-provision-20190905-63.7.2.x86_64.rpm
        
19.  导航到控制台中的“Compute（计算）”和“Instance Pools（实例池）”，并收集集群网络池的所有 IP 地址。或者，如果您的专用子网上没有其他操作，请在堡垒上使用此命令。
    
        for i in `nmap -sL Private_Subnet_CIDR | grep "Nmap scan report for" | grep ")" | awk '{print $6}'`;do echo ${i:1:-1} >> /tmp/ips; done
        
20.  通过以下命令安装预配工具：
    
        ips=`cat /tmp/ips`
        /opt/oci-hpc/setup-tools/cluster-provision/hpc_provision_cluster_nodes.sh -p -i /home/opc/.ssh/id_rsa $ips
        

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月