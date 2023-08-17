# 访问集群

## 简介

在本练习中，您将访问您的集群。

估计实验室时间：10 分钟

## 任务：访问集群

1.  堡垒的公共 IP 地址位于左下方的 "Outputs"（输出）下方菜单中。如果您在主菜单中导航到实例，则还会找到堡垒实例和公共 IP。
    
2.  也可以在此处找到用于访问计算机的私钥。在计算机上的文件中复制文本，让我们说/home/user/key：
    
        chmod 600 /home/user/key 
        ssh -i /home/user/key opc@ipaddress 
        

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月