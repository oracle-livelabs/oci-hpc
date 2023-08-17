# 访问 GPU 节点

## 简介

在本练习中，您将访问 GPU 节点。

估计实验室时间：5 分钟

## 任务：访问 GPU 节点

1.  在资源管理器中成功完成作业后，可以在左下方的**输出**菜单中找到 GPU 节点的公共 IP 地址和私钥。
    
2.  将私钥复制到本地计算机上，更改密钥的权限并登录到实例：
    
        chmod 400 /home/user/key
        ssh -i /home/user/key opc@ipaddress
        
        
3.  登录到 GPU 节点后，您可以从 /mnt/block 运行 Gromacs。有关如何在 GPU 实例上运行 Gromacs 的特定命令，请参阅 README.md 文件。
    

## 确认

*   **作者** - 高性能计算团队
*   **贡献者** - Chris Iwicki、Harrison Dvoor、Gloria Lee、Selene Song、Bre Mendonca、Samrat Khosla
*   **上次更新者/日期** - Samrat Khosla，2020 年 10 月