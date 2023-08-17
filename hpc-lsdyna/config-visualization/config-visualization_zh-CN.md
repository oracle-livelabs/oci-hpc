# 配置可视化

## 简介

HPC 负载通常需要可视化工具来调度、监视或分析模拟输出。在这些情况下，通常需要创建一个 GPU 可视化节点以实现最佳分辨率和后期处理。默认情况下，未在 OCI 实例上安装 GUI；但是，可以使用 VNC 或 X11 远程显示协议轻松配置 GUI。以下各小节将介绍如何使用 TurboVNC 和 OpenGL 在公共子网中创建 GPU 可视化节点。

估计实验室时间：25 分钟

## 任务 1：设置堡垒上的 VNC

默认情况下，只有通过 SSH 在控制台模式下访问 Oracle Linux 计算机。如果要查看图形界面，则需要设置 VNC 连接。以下脚本将适用于默认用户操作。vnc 会话的口令设置为 "HPC\_oci1"，但在下一组命令中可以对其进行编辑。如果您当前未通过 SSH 连接到头节点，请执行此操作，因为这些命令需要在头节点上运行。

        sudo yum -y groupinstall "Server with GUI"
        sudo yum -y install tigervnc-server mesa-libGL
        sudo systemctl set-default graphical.target
        sudo cp /usr/lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:1.service
        sudo sed -i 's/<USER>/opc/g' /etc/systemd/system/vncserver@:1.service
        sudo sed -ie '/^ExecStart=/a PIDFile=/home/opc/.vnc/%H%i.pid' /etc/systemd/system/vncserver@:1.service
        sudo mkdir /home/opc/.vnc/
        sudo chown opc:opc /home/opc/.vnc
        echo "password" | vncpasswd -f > /home/opc/.vnc/passwd
        chown opc:opc /home/opc/.vnc/passwd
        chmod 600 /home/opc/.vnc/passwd
        sudo systemctl start vncserver@:1.service
        sudo systemctl enable vncserver@:1.service
    

## 任务 2：添加 GPU 实例

下面是使用 OpenGL 在 OCI 上增强 GPU 使用场景的步骤 - 有关详细信息，请参阅博客。

1.  在控制台中，依次导航到“Compute（计算）”和“Instance（实例）”。
    
2.  为可视化节点创建计算实例：
    
    *   选择所需的 AD
    *   选择所需的 GPU 配置（VM 或 BM）
    *   指定与 GPU 兼容的 Oracle Linux 映像 最新 Oracle Linux 映像将自动启用 GPU。
    *   选择集群网络 VCN 和公共子网
    *   复制 - 粘贴您的公共 SSH 密钥
    *   单击“创建”。
3.  等待实例预配，然后通过以下方式登录到实例：
    
        ssh opc@<public ip> -i <private key> 
        
4.  安装 X 窗口系统、显示管理器 (GNOME/GDM) 和桌面环境 (MATE)：
    
        sudo yum groupinstall "X Window System"
        sudo yum install gdm
        sudo yum groupinstall "MATE Desktop"    
        
5.  安装 VNC 服务器和 VirtualGL。请注意，VirtualGL 是一个开源工具包，允许任何 Linux 或 Unix 控制台使用完全硬件加速运行 OpenGL 应用程序。
    
        sudo yum install https://downloads.sourceforge.net/project/virtualgl/2.6.3/VirtualGL-2.6.3.x86_64.rpm
        sudo yum install https://downloads.sourceforge.net/project/turbovnc/2.2.4/turbovnc-2.2.4.x86_64.rpm    
        
6.  配置 X 服务器以启用虚拟会话的 GPU 共享。运行以下命令：
    
        sudo nvidia-xconfig --use-display-device=none --busid="PCI:4:0:0"
        
7.  配置 X 服务器以启用虚拟会话的 GPU 共享。运行以下命令：
    
        sudo vglserver_config -config -s -f -t
        
8.  要避免在启动屏幕保护程序时被锁定，请将本地用户密码设置为稍后可以使用的内容：
    
        sudo passwd opc
        
9.  将 VNC 密码更改为可用于登录的内容：
    
        vncpasswd
        
10.  重新启动 X 服务器：
    
        kill $(pgrep Xvnc)
        vncserver
        
11.  启用和启动 GDM：
    
        systemctl enable gdm --now
        
12.  启动 VNC 服务器：
    
        /opt/TurboVNC/bin/vncserver -wm mate-session
        
13.  如果要直接访问 VNC 服务器而不使用 SSH 转发，请确保安全列表允许端口 5901/tcp 上的连接。
    
    *   在控制台中，依次导航到“Networking（网络）”和“Virtual Cloud Networks（虚拟云网络）”。
    *   依次选择“Subnets（子网）”和“public subnet（公共子网）”。
    *   在默认安全列表中，添加包含以下详细信息的入站规则：
        *   无状态：否
        *   源类型：CIDR
        *   源 CIDR：0.0.0.0/0
        *   IP 协议：TCP
        *   源端口范围：全部
        *   目标端口范围：5901 注：标准 VNC 端口为 5900 加上显示编号（例如，5901 表示 ：1,5902 表示 ：2）
14.  允许在本地防火墙设置中进行访问，如下所示：
    

     sudo firewall-cmd --zone=public --permanent --add-port=5901/tcp
     sudo firewall-cmd --reload
     ```
    15. Open TurboVNC or TigerVNC client. Enter the IP address connection as :1
    
    
    ## Acknowledgements
    * **Author** - High Performance Compute Team
    * **Contributors** -  Chris Iwicki, Harrison Dvoor, Gloria Lee, Selene Song, Bre Mendonca, Samrat Khosla
    * **Last Updated By/Date** - Samrat Khosla, October 2020