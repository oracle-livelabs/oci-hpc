# 通过 VNC 设置和访问

## 简介

在本练习中，我们将逐步完成通过 VNC 客户端设置和访问 Ubuntu 实例所需的步骤（此处为：TigerVNC）。

估计研讨会时间：15 分钟

### 目标

*   通过 SSH 连接到您的计算实例
*   在 Ubuntu 实例上设置和配置 VNC
*   通过 VNC 客户端访问您的计算实例（例如 TigerVNC）

### 你需要什么？

*   完成实验室 1 和实验室 2
*   [将入站规则添加到 VCN 安全列表](https://docs.oracle.com/en-us/iaas/Content/Network/Concepts/securitylists.htm#Using)，以允许端口 5900+N 的 TCP 流量，其中 N 是显示号（通常为：0 表示物理显示）。![](images/vncseclist.PNG " ")

## 任务 1：通过 SSH 连接到计算实例

1.  打开所选的终端（也称为命令行或控制台）。在本示例中，我们在本地 Windows 计算机上工作并使用 Windows PowerShell 作为终端。
    
2.  请注意，在区间的实例列表中，您将在**计算 -> 实例**下找到的 Ubuntu 实例的公共 IP 地址。
    
3.  在您的终端中，导航到您在创建 Ubuntu 计算实例期间使用的 SSH 密钥对所在的文件夹。在这种情况下，文件夹称为 _ssh_ 。
    

       <copy>cd .ssh</copy>
    

4.  输入以下命令以访问 Ubuntu 计算实例。您需要将 _private-key_ 替换为 OpenSSH 私有密钥，将 _public ip_ 替换为 Ubuntu 实例的公共 IP 地址。

       <copy>ssh -i private-key ubuntu@public_ip</copy>
    

## 任务 2：在 Ubuntu 实例上设置和配置 VNC

1.  通过在终端中运行以下命令安装 VNC 服务器：

       <copy>sudo apt update</copy>
    

    <copy>sudo apt -y install vnc4server</copy>
    

2.  安装桌面环境。我们将通过输入以下命令安装 Xfce 桌面环境（但您可以选择使用其他桌面环境）：

       <copy>sudo apt install xfce4 xfce4-goodies</copy>
    

3.  运行 vncpasswd 命令为 VNC 服务器设置密码。

       <copy>vncpasswd</copy>
    

出现提示时，输入密码并通过再次输入来验证密码。

4.  使用以下命令启动 VNC 服务器：

       <copy>vncserver :1</copy>
    

将显示以下输出。 ![](images/vncserver.PNG " ")

5.  使用以下命令中止 VNC 服务器：

       <copy>vncserver -kill :1</copy>
    

![](images/vnc-kill.PNG " ")

6.  VNC 配置文件位于 ~/.vnc/xstartup 上。使用您喜爱的文本编辑器进行编辑。我们将使用 vim。

       <copy>sudo vim ~/.vnc/xstartup</copy>
    

7.  在文件的末尾添加以下行：

       <copy>exec /usr/bin/startxfce4 &</copy>
    

如果使用 vim：按 **A** 键在末尾附加文本并复制粘贴以上命令。通过按 ESC 键切换到命令模式。键入 **：wq** - 保存并退出。

8.  通过执行以下命令启动 VNC 服务器。

       <copy>vncserver :1</copy>
    

![](images/startvncserver.PNG " ")

9.  通过执行以下命令关闭与实例的 SSH 连接：

       <copy>exit</copy>
    

## 任务 3：连接到 VNC 桌面

您可以使用 VNC 客户机（例如 TigerVNC）和 SSH 隧道连接到远程桌面。

1.  使用以下命令创建到 VNC 服务器的 SSH 隧道（其中，private-key 是您的专用 OpenSSH 密钥，public\_ip 是来自 Ubuntu 计算实例的专用 IP 地址）：

    <copy>ssh -i private-key ubuntu@public_ip -C -L 5901:127.0.0.1:5901</copy>
    

2.  安装 vncviewer 客户机，在本例中将为 TigerVNC。

       <copy>sudo apt install tigervnc-viewer</copy>
    

3.  在本地计算机上下载并安装 VNC 客户机（例如 TigerVNC）。使用 VNC 客户机连接到 **127.0.0.1：5901** 。 ![](images/tigervnc.PNG " ")
    
4.  当提示输入口令时，输入在 VNC 服务器配置期间创建的口令。 ![](images/tigervncpwd.PNG " ")
    

您的远程 Ubuntu 桌面将打开。 ![](images/vncdesktop.PNG " ")

## **确认**

*   **创建者/日期** - Oracle 建议到生产计划 HPC 解决方案架构师 Maria Patelkou，2021 年 3 月
*   **上次更新者/日期** - Oracle 建议到生产计划 HPC 解决方案架构师 Maria Patelkou，2021 年 3 月