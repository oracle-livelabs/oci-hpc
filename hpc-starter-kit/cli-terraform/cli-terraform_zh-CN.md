# 使用 OCI CLI 命令和 Terraform 处理 HPC

## 简介

`ocihpc` 是用于简化 Oracle Cloud Infrastructure (OCI) 中 HPC 应用程序部署的工具。通过 HPC 命令行界面 (Command Line Interface，CLI)，您可以使用单个命令通过专用裸金属 HPC 计算实例和 Mellanox RDMA 网络部署任意规模的 HPC 集群。无需了解 Terraform 或 Oracle Cloud Infrastructure Resource Manager 即可使用 HPC CLI。它基于核心 Oracle Cloud Infrastructure CLI 构建，并包含预构建的 Terraform 脚本。

使用 HPC CLI 的好处包括：

1.  通过单个命令快速启动包含基于融合以太网的 RDMA (RoCE) v2 的完整计算集群。
2.  无需了解启动基本集群网络所需的 Terraform 或 Oracle Cloud Infrastructure Resource Manager。
3.  支持消息传递接口 (Message Passing Interface，MPI) 部署包括一组完整的软件包，用于运行与 RDMA 的并行处理，包括使用 Open MPI、Intel MPI 和平台 MPI 的 Mellanox OFED。
4.  可定制 您可以执行自己的 terraform 脚本或添加到随工具提供的现有脚本来安装您自己的应用程序。

估计实验室时间：60 分钟

### 目标

作为一名开发人员或数据工程师，

1.  使用 Oracle 命令行界面部署 HPC 集群网络
2.  通过 Cli 查看、运行和管理堆栈

### 前提条件

1.  具有足以执行 Terraform 堆栈的用户身份证明的 Oracle Cloud Infrastructure 账户。
2.  生成的 SSH 密钥

### 策略

您在 `ocihpc` 中使用的 OCI 用户账户应该为 OCI 资源管理器配置必要的策略。有关所需策略的信息，请查看[此链接](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/managingstacksandjobs.htm)。

## 任务 1：在 macOS/Linux 上安装 ocihpc

1.  使用以下命令下载最新发行版并提取：
    
        $ curl -LO https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_darwin_x86_64.tar.gz
        
2.  解压缩您在步骤 1 中下载的文件。
    
        $ gunzip -c ocihpc_v1.0.0_darwin_x86_64.tar.gz | tar xopf -
        
3.  使 ocihpc 二进制文件成为可执行文件。
    
        $ chmod +x ./ocihpc 
        
4.  将 ocihpc 二进制文件移动到 PATH。
    
        $ sudo mv ./ocihpc /usr/local/bin/ocihpc 
        
5.  测试其运行情况。
    
        $ ocihpc version 
        

## 任务 2：在 Windows 上安装 ocihpc

1.  从[此链接](https://github.com/oracle-quickstart/oci-ocihpc/releases/download/v1.0.0/ocihpc_v1.0.0_windows_x86_64.zip)下载最新版本并将其提取。
    
2.  将 ocihpc 二进制文件添加到 PATH。
    
3.  测试其运行情况。
    
        $ ocihpc.exe version 
        

## 任务 3：在命令行上创建 ssh 密钥对

如果您使用的是窗口，请参阅[此链接](https://docs.cloud.oracle.com/en-us/iaas/Content/GSG/Tasks/creatingkeys.htm)。

1.  打开 shell 或终端以输入命令。
    
2.  导航到 .oci 文件夹
    
        $ cd Users/enjli/.ssh
        
3.  如果您尚未创建文件夹，则创建 .ssh 目录来存储身份证明：
    
        $ mkdir Users/enjli/.ssh
        
4.  如果在文件夹中看不到任何 id\_rsa 密钥对，请输入以下命令并在出现提示时提供名称和密码短语。将使用默认值创建密钥：RSA 密钥为 2048 位。
    
        $ ssh-keygen
        

## 任务 4：生成 API 签名密钥

您的 API 请求将使用您的私钥签名，Oracle 将使用该公钥验证请求的真实性。如果您使用的是窗口，请参阅[此链接](https://docs.cloud.oracle.com/en-us/iaas/Content/API/Concepts/apisigningkey.htm#Other)。

1.  如果您还没有安装，则创建 .oci 目录来存储身份证明：
    
        $ mkdir Users/enjli/.oci
        
2.  使用以下命令生成私有密钥。
    
        $ openssl genrsa -out ~/.oci/oci_api_key.pem 2048
        
3.  确保只有您可以读取私有密钥文件：
    
        $ chmod go-rwx ~/.oci/oci_api_key.pem
        
4.  生成公共密钥：
    
        $ openssl rsa -pubout -in ~/.oci/oci_api_key.pem -out ~/.oci/oci_api_key_public.pem
        

## 任务 5：将公共密钥添加到 Oracle Cloud Infrastructure

现在您拥有私有/公钥组合，必须在用户设置下将其添加到 OCI 控制台：

1.  导航到包含 `oci_api_key_public.pem` 的 .oci 文件夹，复制公共密钥。
    
2.  登录到 OCI 控制台，然后单击“Menu（菜单）”并选择“Identity and Users（身份和用户）”。选择用户并导航到“用户详细信息”页面。
    
3.  单击“API 密钥”部分下的“添加公钥”。![市场](images/ResourcesMenu.png) ![市场](images/APIKeys.png)
    
4.  粘贴您在添加公钥中从 CLI 复制的公钥 ![市场](images/AddPublicKey.png)
    

## 任务 6：配置

此步骤介绍了 CLI 所需的配置，包括可选配置，使您可以扩展 CLI 功能。

1.  在使用 CLI 之前，必须在 .oci 文件夹中创建配置文件，其中包含使用 Oracle Cloud Infrastructure 账户所需的身份证明。您可以使用设置对话框或通过文本编辑器手动创建此文件。
    
2.  运行 _ocihpc configure_ 以检查您是否具有访问 OCI 的有效配置。该工具将指导您创建配置。
    

将在配置文件写入到的位置通知您：

_配置文件保存到：/Users/enjli/.oci/config_

要创建配置文件，您需要：1。您的用户 OCID（位于屏幕右上角的概要信息区域中的“用户设置”>“用户信息”选项卡）2。租户 OCID（“管理”>“租户详细信息”>“租户信息”选项卡），3。您要解决的区域（例如 us-phoenix-1、us-ashburn-1 等）、4。用户的指纹（在用户信息选项卡中找到），5。API 私有签名密钥的路径。

示例 `config` 文件如下所示： ![市场](images/oci_config.png)

## 任务 7：列表

您可以通过运行 `ocihpc list` 获取可用堆栈的列表。

示例：

    ```
    sh
    $ ocihpc list
    
    List of available stacks:
    
    ClusterNetwork
    Gromacs
    OpenFOAM
    ```
    

## 任务 8：初始化

1.  创建将用作部署源的文件夹。

重要说明：每个堆栈使用不同的文件夹。请勿在同一文件夹中初始化多个堆栈。否则，该工具将覆盖以前的工具。

2.  转到该文件夹并运行 `ocihpc init <stack name>`。`ocihpc` 会将必要的文件下载到该文件夹。
    
        $ mkdir ocihpc-test
        $ cd ocihpc-test
        $ ocihpc init --stack ClusterNetwork
        
        Downloading stack: ClusterNetwork
        
        ClusterNetwork downloaded to /Users/enjli/ocihpc-test/
        
        

**重要提示**：在运行 ocihpc deploy 命令之前，编辑 /Users/enjli/ocihpc-test/config.json 文件的内容

## 任务 9：部署

1.  部署之前，需要更改 `config.json` 文件中的值。变量取决于您部署的堆栈。群集网络的示例 `config.json` 如下所示：
    
        {
          "variables": {
            "ad": "kWVD:PHX-AD-1",
            "bastion_ad": "kWVD:PHX-AD-2",
            "bastion_shape": "VM.Standard2.1",
            "node_count": "2",
            "ssh_key": "ssh-rsa AAAAB3NzaC1yc2EAAAA......W6 enjli@enjli-mac"
          }
        }
        
2.  要修改 `config.json` 文件，请导航到新创建的目录（本例中为 ocihpc-test），然后使用文本编辑器或记事本打开“config.json”文件。 ![市场](images/config_json.png)
    

请注意，这不是我们在步骤 1 中配置的同一配置文件。

对于此配置文件，我们需要：

1.  我们的租户中包含 HPC 资源的可用性域信息（“管理”>“租户详细信息”>“向下滚动到“服务限制”部分 >“计算”>“并向下滚动以查找“BM.HPC2.36”）- 在下面的屏幕截图中，我们总共有 6 台 BM.HPC2.36 计算机要用在 AD-2 中，其中 0 台当前正在使用。 ![市场](images/hpc_resource.png)
    
2.  只要有资源（VM.standard2.1 配置）堡垒配置应该已填充，就可以选择任何 AD - VM.Standard2.1
    
3.  节点计数 - 在本练习中，我们将使用 2，以便使用所有 HPC 资源
    
4.  我们的公共 SSH 密钥
    

**附注**

1.  更改 `config.json` 中的值后，可以使用 `ocihpc deploy <arguments>` 部署堆栈。此命令将在 Oracle Cloud Resource Manager 上创建堆栈并使用它部署堆栈。
    
2.  对于支持的堆栈，可以通过将要部署的节点添加到 `ocihpc deploy` 命令来设置其数量。如果堆栈不支持它，或者如果您未提供值，则工具将使用默认数字进行部署。
    
3.  例如，以下命令将部署具有 5 个节点的集群网络：
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
4.  该工具将生成包含 `<stack name>-<current directory>-<random-number>` 的部署名称。
    
    示例：
    
        $ ocihpc deploy --stack ClusterNetwork --node-count 5 --region us-ashburn-1 --compartment-id ocid1.compartment.oc1..6zvhnus3q
        
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 0sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 17sec]
        Deploying ClusterNetwork-ocihpc-test-7355 [0min 35sec]
        ...
        

## 任务 10：连接

部署完成后，您将看到可以连接到以下位置的堡垒/头节点 IP：

    ```
    $ Successfully deployed ClusterNetwork-ocihpc-test-7355
    
    $ You can connect to your head node using the command: ssh opc@$123.221.10.8 -i <location of the private key you used>
    ```
    

还可以通过运行 `ocihpc get ip` 命令获取连接详细信息。

## 任务 11：管理

此外，您可以使用 cli 命令轻松管理和跟踪资源：

1.  要生成在特定区间中部署的所有堆栈的列表，请执行以下操作：`oci resource-manager job list -c [OCID OF COMPARTMENT]`
    
2.  要生成堆栈或区间中作业的列表（按创建时间排序）：`oci resource-manager job list -c [COMPARTMENT OCID]`
    
3.  要查看作业以及作业详细信息，请执行以下操作：`oci resource-manager job get --job-id [OCID OF THE JOB]`
    
4.  要以 JSON 格式查看指定作业的日志条目，请执行以下操作：`oci resource-manager job get-job-logs --job-id [OCID OF THE JOB]`
    
5.  要将堆栈及其关联的作业移到其他区间中，请执行以下操作：`oci resource-manager stack change-compartment -c [OCID OF NEW COMPARTMENT] --stack-id [OCID OF THE STACK]`
    

## 任务 12：删除

完成部署后，可以通过更改为堆栈文件夹并运行 `ocihpc delete --stack <stack name>` 来删除部署。

    Example:
    ```
    $ ocihpc delete --stack ClusterNetwork
    
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 0sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 17sec]
    Deleting ClusterNetwork-ocihpc-test-7355 [0min 35sec]
    ...
    
    Successfully deleted ClusterNetwork-ocihpc-test-7355
    ```
    

全部完成！您已成功使用 OCI CLI 工具部署了第一个高性能计算实例。

_您现在可以继续下一练习_

这是有关管理高性能计算实例的详细信息。有关完整的命令参考，请查看[此处](https://docs.cloud.oracle.com/en-us/iaas/Content/Compute/Tasks/managingclusternetworks.htm?Highlight=hpc)的 OCI 文档。

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者/日期** - Harrison Dvoor，2020 年 10 月

# <<<<<<< 标头

> > > > > > > 上游/主节点