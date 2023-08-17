# 访问 VNC

## 简介

在本练习中，您将访问 VNC。

估计实验室时间：10 分钟

## 任务：访问 VNC

1.  我们将通过 SSH 隧道连接到实例。在您的计算机上，使用下面的 ssh PORT 进行连接将是 5900 + N 的结果编号。N 是显示编号，如果 N 的输出是 1，PORT 是 5901，如果输出是 9，PORT 是 5909 public\_ip 是运行 VNC 服务器的 headnode 的公共 IP 地址。如果使用了以前的指令，则端口将为 5901
    
        <copy>
            ssh -L 5901:127.0.0.1:5901 opc@public_ip
        </copy>
        
        
2.  现在，您可以使用本地主机：N 作为 VNC 服务器的任何 VNC 查看器以及您在 vnc 安装期间设置的密码进行连接。您可以选择您喜欢的 VNC 客户机，或者使用此指南在本地计算机上安装：
    

*   [Windows-TigerVNC](https://github.com/TigerVNC/tigervnc/wiki/Setup-TigerVNC-server-%28Windows%29)
*   [MacOS/Windows - RealVNC](https://www.realvnc.com/en/connect/download/vnc/)

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月