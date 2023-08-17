# 预配 GPU 实例

## 简介

在此实验室中，我们将完成在 OCI 上预配 GPU 实例的必需步骤，并基于它运行 Ubuntu。

**注：**在 OCI 上预配 GPU 实例需要 PAYG 账户。如果您使用的是 Oracle 30 天免费试用版，则可以使用所有可用的 VM 和 BM 实例。

估计研讨会时间：15 分钟

### 目标

*   登录到 Oracle Cloud
*   在 OCI 上预配 GPU 实例

### 你需要什么？

*   OCI 上属于用户组的用户（具有用户名和密码）。检查[管理用户](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingusers.htm)页以了解**创建用户**的方式以及如何使用控制台**将用户添加到组**的方式。
*   Cloud 账户名称 - 租户的名称（由管理员或 Oracle Cloud 欢迎电子邮件提供）
*   用户组。检查[管理组](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managinggroups.htm#three)页，了解如何使用控制台**创建组**。
*   为允许用户启动计算实例的组分配策略。请查看[常见策略](https://docs.oracle.com/en-us/iaas/Content/Identity/Concepts/commonpolicies.htm#top)页，了解您需要使用控制台**让用户启动计算实例**的策略。
*   用户的区间。查看[管理区间](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingcompartments.htm#uscons)页，了解如何使用控制台**创建区间**。
*   检查要预配 GPU 实例的区域中每个可用性域允许的最大 GPU 实例数的服务限制。要提高服务限制，请按照[服务限制](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/servicelimits.htm#top)页面中的步骤操作。

## 任务 1：登录 Oracle Cloud

如果您已从 Oracle Cloud 注销，请使用以下步骤重新登录。

1.  转到 [cloud.oracle.com](https://cloud.oracle.com) 并输入您的 Cloud 账户名称，然后单击**下一步**。这是您在上一节中创建账户时选择的名称。不是你的电子邮件地址。如果您忘记了名称，请查看确认电子邮件。
    
    ![](images/cloud-oracle.png " ")
    
2.  在 _Oracle Cloud Infrastructure Direct Sign-In_ 后展开箭头以显示登录输入字段。
    
    ![](images/cloud-login-tenant.png " ")
    
3.  输入 Cloud 账户身份证明并单击**登录**。您的用户名是您的电子邮件地址。密码是您在注册账户时选择的密码。
    
    ![](images/oci-signin.png " ")
    
4.  您现在已登录 Oracle Cloud！
    
    ![](images/oci-console-home-page.png " ")
    

## 任务 2：启动 GPU 实例

1.  登录后，单击**快速操作**部分中的**创建 VM 实例**按钮。
    
    ![](images/click-create-vm-instance.png " ")
    

或者，可以单击左上方的服务菜单，然后单击**计算** -> **实例**。此时将打开一个新页面，其中显示您单击的蓝色**创建实例**按钮。

2.  填写您的计算实例的名称。
    
    ![](images/fill-in-name.PNG " ")
    
3.  从下拉列表中选择区间。
    
    ![](images/select-compartment.PNG " ")
    
4.  在选择可用性域之前，请查看**监管** -> **限制、限额和使用量**，在其中，所选区域的可用性域具有可用的 GPU 实例。选择将在其中预配计算实例的可用性域。
    
    ![](images/select-AD.PNG " ")
    
5.  单击**更改图像**按钮。
    
    ![](images/change-image.PNG " ")
    
    选择要用于计算实例的映像，然后单击**选择映像**按钮。我们将选择 Faceswap 推荐的 Ubuntu 18。04，但任何其他 Linux 发行版和 Windows 操作系统也可能起作用。
    
    ![](images/select-image.PNG " ")
    
6.  单击**更改配置**按钮。
    
    ![](images/change-shape.PNG " ")
    
    选择**虚拟机**和**专业和传统**。您可以在其中找到 GPU 实例和**始终免费** VM.Standard.E2.1。微配置（如果您没有运行 GPU 实例的 PAYG 账户）。
    
    ![](images/select-VM-SL.PNG " ")
    
    选择要预配、读取和勾选的 GPU 实例。_我已查看并接受以下文档：Oracle 和 Nvidia 使用条款_，然后单击**选择配置**按钮。在这种情况下，它基于具有 1 个 GPU 的 P100（NVIDIA Pascal GPU 体系结构）的 VM.GPU2.1。您还可以基于 V100（NVIDIA Volta GPU 架构）和 A100（NVIDIA Ampere GPU 架构）请求虚拟和裸金属机的服务限制。
    
    ![](images/select-GPU.PNG " ")
    
7.  如果您已在区间中创建 VCN，请选择要放置计算实例的 VCN 和子网。否则，您可以单击**创建新虚拟云网络**，为 VCN 选择名称和区间，为公共子网选择名称。您可以保留默认 CIDR 块或对其进行调整。您可以选择为实例分配公共 IP 地址。通过分配公共 IP 地址，可以从 Internet 访问此实例。如果您不确定是否需要公共 IP 地址，可以稍后始终分配一个。
    
    ![](images/create-vcn.PNG " ")
    
8.  如果要使用在_练习 1 - 创建 SSH 密钥_中创建的 SSH 公共密钥对和私有密钥对，可以单击**选择公共密钥文件**并选择 SSH 公共密钥文件，或者单击**粘贴公共密钥**并将 SSH 公共密钥粘贴到字段中。您还可以在 OCI 上创建 SSH 公共密钥和私有密钥并将其保存到本地计算机。请注意，如果不提供 SSH 密钥，您将无法使用 SSH 连接到实例。
    
    ![](images/add-SSH.PNG " ")
    
9.  最后一步是指定引导卷大小，选择是否使用传输中加密以及如何管理加密密钥。对于 Linux Syxtems，可以采用默认引导卷大小 46.6 GB，也可以增加到最多 32 TB。您可以不选中所有框，然后单击**创建**（以最终创建计算实例）。
    

![](images/boot-volume.PNG " ")

几分钟后，您的 GPU 计算实例会将状态更改为 **RUNNING**

![](images/instance-running.PNG " ")

## **确认**

*   **创建者/日期** - Oracle 建议到生产计划 HPC 解决方案架构师 Maria Patelkou，2021 年 3 月
*   **上次更新者/日期** - Oracle 建议到生产计划 HPC 解决方案架构师 Maria Patelkou，2021 年 3 月