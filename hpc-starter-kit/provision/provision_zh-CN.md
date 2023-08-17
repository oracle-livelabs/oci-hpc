# 从 Oracle 市场映像预配 HPC 集群

## 简介

在此实验室中，您将从 OCI 市场预配 HPC 集群。OCI Marketplace 为各种行业解决方案提供一站式服务，可以在我们的云控制台中轻松部署。在这里，您将利用此市场进行自动 HPC 集群部署。市场中的模板提供了一个高性能计算解决方案的基础，包括启动工作量时所需的所有组件。随着这些实验室的进步，您将看到第三方应用程序如何集成和利用这样的基线来加速您的工作流。

估计实验室时间：60 分钟

### 目标

作为一名开发人员或数据工程师，

*   导航到 OCI 市场
*   从 OCI 市场部署 HPC 集群映像
*   在 OCI 上查看和运行堆栈

### 前提条件

*   具有创建实例 VM 标准 2.1 或 BM.HPC2.36 配置的权限的 Oracle Cloud Infrastructure 账户。

## 任务 1：导航到 OCI 市场

1.  单击左上角的汉堡菜单（ 备注）。 ![市场](images/click_hamburger.png)
    
2.  向下滚动，然后单击“市场”。 ![市场](images/click_marketplace.png)
    
3.  在市场中搜索 **HPC** ，然后选择 HPC 集群。 ![市场](images/marketplace.png)
    
4.  查看并接受 **Oracle 标准条款和限制**，然后单击**启动堆栈**。 ![市场](images/launch_stack.png)
    

## 任务 2：配置 HPC 堆栈

启动堆栈后，您将重定向到“Create Stack Wizard（创建堆栈向导）”，您可以在其中填充堆栈信息。

1.  在**堆栈信息**中，为堆栈指定名称，_（可选）_指定在当前未所在的区间中部署。 ![市场](images/stack_p1.png)
    
2.  为计算配置和堡垒选择所需的 **AD** 。
    
3.  复制 - 粘贴您的公共 **ssh key** ![市场](images/stack_p2_1.png)
    
4.  键入集群的**计算实例**数 ![市场](images/stack_p2_2.png)
    
5.  在**网络选项**下，保留默认值 ![市场](images/stack_p2_3.png)
    
6.  单击**下一步**以查看配置。 ![](./images/stack_p3.png)
    
7.  单击**创建**
    

**注：**预配堆栈大约需要 15 分钟。

## 任务 3：查看正在运行的堆栈

创建堆栈后，它将将您重定向到可在其中查看堆栈作业运行的页。它应该如下所示。您可以在此处查看：

1.  **日志**
2.  传递给 Terraform 脚本或由其生成的**变量**
3.  由 Terraform 脚本预配的**关联资源**
4.  **输出**
5.  作业的**查看状态**

![市场](images/stack_detail_provisioning.png)

要导航到此页，可在其中查看当前区间中的所有堆栈，请单击左上方和**解决方案和平台**下方的汉堡包菜单（实验）将鼠标悬停在**资源管理器** > **堆栈**上。

![市场](images/nav_resource_manager.png)

全部完成！这将完成从 Oracle 市场映像预配 HPC 集群的演示。

_您现在可以继续下一练习_

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca
*   **上次更新者/日期** - Harrison Dvoor，2020 年 10 月

# <<<<<<< 标头

> > > > > > > 上游/主节点